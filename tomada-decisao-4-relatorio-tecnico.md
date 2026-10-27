# 📄 RELATÓRIO TÉCNICO DE OBSERVABILIDADE, ARQUITETURA E INFRAESTRUTURA

**EMISSOR:** Núcleo de Engenharia de Plataforma & NOC (Network Operations Center)  
**EMPRESA:** HealthTech Nexus Soluções em Saúde S.A.  
**APLICAÇÃO:** Plataforma SaaS NexusCare (Release Candidate v5.2)  
**DATA E HORA DA EMISSÃO:** Sexta-feira, 24 de Outubro de 2026 — 23h45 (BRT)  
**CLASSIFICAÇÃO:** Técnico / Restrito / Uso Interno  
**DESTINATÁRIOS:** Diretoria de TI, Engenheiros de SRE e Lideranças Técnicas de Operações  

---

## 1. VISÃO GERAL DA ARQUITETURA DO SISTEMA (CONTEXTO DA APLICAÇÃO)

A plataforma **NexusCare v5.2** é estruturada em uma arquitetura de microsserviços distribuída, conteinerizada e orientada a eventos, implantada na nuvem Amazon Web Services (AWS), região `us-east-1` (N. Virginia). O sistema opera no modelo *multi-tenant*, atendendo **85 grandes redes hospitalares privadas** (abrangendo mais de 420 hospitais físicos e 6 milhões de prontuários ativos de pacientes).

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                             CAMADA DE BORDA & DNS                                │
│          AWS Route 53 ──► AWS CloudFront (CDN) ──► AWS WAF (Firewall)            │
└────────────────────────────────────────┬─────────────────────────────────────────┘
                                         │
                                         ▼
┌──────────────────────────────────────────────────────────────────────────────────┐
│                         API GATEWAY & AUTENTICAÇÃO                               │
│            Kong Enterprise Gateway v3.4 (mTLS, Rate Limiting, Routers)           │
└────────────────────────────────────────┬─────────────────────────────────────────┘
                                         │
                                         ▼
┌──────────────────────────────────────────────────────────────────────────────────┐
│                      CAMADA DE APLICAÇÃO (KUBERNETES EKS)                        │
│  ┌───────────────────────┐  ┌───────────────────────┐  ┌──────────────────────┐  │
│  │     pep-service       │  │  prescription-service │  │ integration-service  │  │
│  │  (Java 17 / Spring)   │  │  (Assinatura Digital) │  │  (HL7 / FHIR Router) │  │
│  └───────────┬───────────┘  └───────────┬───────────┘  └──────────┬───────────┘  │
│              │                          │                         │              │
│              │        ┌─────────────────┴─────────────────────────┘              │
│              │        │                                                          │
│              ▼        ▼                                                          │
│  ┌────────────────────────────────────────────────────────────────────────────┐  │
│  │                     analytics-service (Novo na v5.2)                       │  │
│  │                     (Módulo de BI e Relatórios Clínicos)                   │  │
│  └────────────────────────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────┬─────────────────────────────────────────┘
                                         │
       ┌─────────────────────────────────┼─────────────────────────────────┐
       │                                 │                                 │
       ▼                                 ▼                                 ▼
┌───────────────────────────┐ ┌───────────────────────────┐ ┌───────────────────────────┐
│     MEN SAGERIA KAFKA     │ │      CACHE REDIS v7.0     │ │   BANCO DE DADOS AURORA   │
│   (AWS MSK - Async Event) │ │  (Cluster Multirregião)   │ │ PostgreSQL 14 (PgBouncer) │
└───────────────────────────┘ └───────────────────────────┘ └───────────────────────────┘
```

### Componentes Principais da Solução:

1. **Camada de Borda e Roteamento (*Edge Layer*):**
   * **AWS Route 53 & CloudFront:** Gerenciamento de DNS global e distribuição de ativos estáticos das telas web.
   * **AWS WAF:** Inspeção de segurança para prevenção de ataques OWASP Top 10 e mitigação básica de volumetria.
   * **Kong Enterprise Gateway (v3.4):** Ponto único de entrada de tráfego HTTPS. Realiza autenticação mútua TLS (mTLS) com equipamentos hospitalares, controle de cota (*Rate Limiting*) e roteamento dinâmico de APIs.

2. **Camada de Aplicação e Processamento (*Kubernetes EKS*):**
   * **Cluster AWS EKS (Kubernetes v1.28):** Orquestrador de contêineres gerenciado rodando em instâncias `m6i.2xlarge`.
   * **`pep-service` (Microsserviço de Prontuário Eletrônico):** Desenvolvido em Java 17 com Spring Boot 3.2. É o núcleo vital da aplicação: processa abertura de fichas de atendimento, anamnese, evolução médica de leitos e prescreve medicamentos para UTI e Pronto-Socorro.
   * **`prescription-service`:** Microsserviço responsável pela validação de ICP-Brasil e assinatura digital de receitas médicas.
   * **`integration-service`:** Roteador de mensagens no padrão HL7/FHIR para comunicação com exames laboratoriais e equipamentos de tomografia/raio-X.
   * **`analytics-service` (Novo Módulo da Release v5.2):** Serviço de Business Intelligence responsável por agregar indicadores hospitalares e gerar relatórios gerenciais históricos para as diretorias clínicas.

3. **Camada de Cache e Mensageria:**
   * **Redis Enterprise Cluster (v7.0):** Cache em memória operado em cluster com replicação primário/secundário. Responsável por armazenar sessões de usuários médicos, dicionários de medicamentos e tabelas de CID-10.
   * **Apache Kafka (AWS MSK v3.5):** Barramento de eventos assíncronos usado para desacoplar ações do prontuário (`pep-service`) da emissão de relatórios (`analytics-service`).

4. **Camada de Persistência e Banco de Dados:**
   * **AWS Aurora PostgreSQL 14.9:** Cluster relacional com 1 Nó Primário (*Writer*) e 2 Nós de Leitura (*Reader Replicas*), rodando em instâncias `db.r6g.4xlarge` (16 vCPUs, 128 GB de RAM por nó).
   * **PgBouncer (v1.20):** Middleware de pooling de conexões configurado em modo *Transaction Pooling*, com limite máximo de 500 conexões simultâneas direcionadas ao PostgreSQL.

---

## 2. HISTÓRICO E BASELINE OPERACIONAL (ÚLTIMOS 30 DIAS)

Durante o período entre **24 de Setembro e 24 de Outubro de 2026**, a infraestrutura de produção apresentou comportamento padrão e dentro das métricas nominais de acordo com o SLA contratado:

### Métricas da Aplicação e Kubernetes (EKS):

| Métrica de Desempenho | Valor Médio Observado | Limite Máximo de SLA | Status |
| :--- | :--- | :--- | :--- |
| **Latência Global (p50)** | 45ms | 80ms | 🟢 Normal |
| **Latência Global (p95)** | 118ms - 135ms | 150ms | 🟢 Normal |
| **Latência Crítica (p99)** | 240ms | 400ms | 🟢 Normal |
| **Vazão Diária (RPS)** | 4.200 req/sec (Pico: 8.800 req/sec) | 12.000 req/sec | 🟢 Normal |
| **Uso de CPU no Cluster EKS** | 22% a 35% | 75% | 🟢 Normal |
| **Heap Memory JVM (`pep-service`)** | 4,2 GB de 8 GB alocados | 7,0 GB | 🟢 Normal |
| **Pausas de Garbage Collection (GC)** | < 15ms (Young Gen) | 50ms | 🟢 Normal |

### Métricas da Camada de Banco de Dados (PostgreSQL):

* **Conexões Simultâneas Ativas no Nó Writer:** Média contínua de 45 a 60 conexões.
* **Volume Total de Dados Armazenados:** 4,8 Terabytes de massa relacional estruturada.
* **Taxa de Operações de E/S (IOPS):** Média de 1.450 IOPS, alcançando picos de 3.200 IOPS nos horários de maior fluxo médico (10h00 e 15h00).
* **Consumo de CPU no Nó Primário (Writer):** Estável em 18%.
* **Buffer Cache Hit Ratio:** 99,4% (Indica que 99,4% das consultas liam dados armazenados diretamente na memória RAM, sem necessidade de leitura em disco SSD).

---

## 3. DETALHAMENTO DO DEPLOY DA RELEASE v5.2 (SEXTA-FEIRA, 22h00)

Em atendimento ao plano de testes e atualização pré-IPO, a equipe de desenvolvimento implantou a **Release Candidate v5.2** na sexta-feira (24/10/2026), às 22h00, utilizando a pipeline automatizada de CI/CD (GitLab CI + ArgoCD em estratégia *Rolling Update*).

### Alterações e Artefatos Implantados:

1. **Atualização no `pep-service` (Build #1482):**
   * Correção de alinhamento visual no formulário de anamnese médica.
   * Adição da validação de algoritmo de CPF e cartão do SUS para novos cadastros.
2. **Habilitação da API v2 de Analytics (`analytics-service`):**
   * Liberação dos endpoints públicos `/api/v2/analytics/dashboard` e `/api/v2/analytics/reports/historical`.
   * Objetivo: Permitir que diretores das redes hospitalares extraiam relatórios financeiros e estatísticos de atendimento em tempo real.
3. **Migração de Esquema no Banco de Dados (`20261024_01_analytics_views.sql`):**
   * Script SQL executado às 22h04 (Concluído em 4,2 segundos sem erros).
   * Criação da View SQL `v_patient_analytics_summary`, responsável por cruzar informações das tabelas `patient_records` (prontuários), `prescriptions` (prescrições) e `billing_events` (faturamento).

---

## 4. TELEMETRIA E COMPORTAMENTO OBSERVADO PÓS-DEPLOY (22h00 ÀS 23h45)

Nas duas horas seguintes à conclusão da implantação da v5.2, os painéis de monitoramento (Datadog APM, Prometheus/Grafana e AWS CloudWatch) registraram desvios no comportamento do sistema:

### A. Camada de Borda e API Gateway
* A latência média global de resposta do sistema subiu de **120ms para 385ms** entre 22h15 e 23h30.
* As requisições direcionadas à nova rota `/api/v2/analytics/dashboard` apresentaram latência individual de **4.850ms (4,85 segundos por requisição)**.

### B. Cluster Kubernetes (EKS) e Pods da Aplicação
* O consumo médio de CPU nos pods do `pep-service` subiu de **28% para 64%**.
* O recurso de escalonamento automático de pods (*HPA - Horizontal Pod Autoscaler*) identificou o aumento de uso de CPU e iniciou a criação de novos pods para distribuir a carga, aumentando a quantidade de réplicas de **12 para 20 pods** às 23h10.
* Os logs das instâncias Java (JVM) **não apresentaram erros de estouro de memória** (`OutOfMemoryError`), porém o tempo de pausa do Garbage Collection subiu de 12ms para 85ms devido à frequência de criação de objetos temporários nas chamadas de relatórios.

### C. Filas de Mensageria (Apache Kafka)
* O tópico `pep-events-v1` registrou um acúmulo temporário (*lag*) de 1.400 mensagens não processadas por volta das 22h45, estabilizando-se após a entrada dos novos pods.

### D. Análise do Banco de Dados PostgreSQL (Slow Query Log)
* O número de conexões ativas no nó primário (*Writer*) saltou de **50 para 185 conexões simultâneas**.
* O uso de CPU da instância do PostgreSQL subiu de **18% para 58%**.
* O *Slow Query Log* (configurado para capturar instruções com tempo superior a 2.000ms) começou a registrar a execução frequente do seguinte trecho de código SQL:

```sql
-- Instrução SQL capturada no Log de Processos Lentos (pid 41820)
SELECT 
    pr.tenant_id,
    pr.patient_category,
    COUNT(pr.id) AS total_patients,
    SUM(be.total_amount) AS total_revenue
FROM patient_records pr
LEFT JOIN billing_events be ON pr.id = be.patient_record_id
WHERE pr.tenant_id = $1 
  AND pr.created_at >= '2026-01-01 00:00:00'
GROUP BY pr.tenant_id, pr.patient_category;
```

#### Análise Técnica da Consulta pelo Otimizador (`EXPLAIN ANALYZE` Preliminar):
* O otimizador de consultas do PostgreSQL está realizando varredura sequencial física (**`Seq Scan`**) na tabela `patient_records` (que possui 4,8 TB de tamanho e mais de 25 milhões de registros).
* **Causa do `Seq Scan`:** A coluna `tenant_id` possui um índice B-Tree isolado, mas a instrução SQL combina a busca com a coluna `created_at`. Como não existe um **índice composto** agrupando `(tenant_id, created_at)`, o banco descarta o índice existente e lê a tabela inteira do disco/RAM a cada requisição enviada pelos painéis de analytics.

---

## 5. ANÁLISE DE INTERDEPENDÊNCIA DOS COMPONENTES

A equipe local de operações cruzou as métricas de todos os subsistemas para mapear a corrente de causa e efeito observada até as 23h45 da sexta-feira:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ 1. A nova rota /api/v2/analytics passa a receber chamadas de relatórios.   │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 2. A ausência de índice composto força o PostgreSQL a fazer Seq Scan.      │
│    Cada consulta leva ~4,8 segundos para ser concluída no banco.            │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 3. O PgBouncer retém as conexões abertas aguardando o fim do Seq Scan.      │
│    O total de conexões ativas no PostgreSQL sobe de 50 para 185.           │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 4. As threads do microsserviço `pep-service` ficam represadas em estado     │
│    de espera (Wait State / Retry Loop) aguardando liberação de conexões.    │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 5. O uso de CPU nos pods Java sobe para 64% devido às threads em espera.    │
│    O HPA do Kubernetes interpreta CPU alta e escala os pods de 12 para 20.  │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ 6. Os 8 novos pods criados abrem mais conexões simultâneas no PgBouncer,    │
│    aumentando a fila de espera e elevando a latência p95 para 385ms.        │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. AVALIAÇÃO PRELIMINAR DE HIPÓTESES TÉCNICAS (SEM SOLUÇÃO DEFINIDA)

A equipe local de SRE encerrou o plantão de acompanhamento da sexta-feira às 23h45 registrando que **a aplicação permanece totalmente operacional e sem erros de sistema** (taxa de erro HTTP 5xx mantida em 0,01%), porém apresentando degradação no tempo de resposta.

Foram catalogadas quatro hipóteses técnicas distintas para justificar a degradação, sem emissão de parecer definitivo sobre qual medida corretiva adotar:

* **Hipótese A (Gargalo de Indexação no Banco de Dados):**  
  A degradação é provocada pelo custo computacional das varreduras sequenciais (`Seq Scan`) na tabela `patient_records`, geradas pelas consultas da nova API de analytics sem índice composto adequado. O risco iminente é que o aumento no volume de acessos nos horários de pico possa exaurir as conexões do banco.

* **Hipótese B (Subdimensionamento do Pool de Conexões - PgBouncer):**  
  O limite de conexões configurado no middleware PgBouncer ou no PostgreSQL pode estar subdimensionado para atender simultaneamente as requisições operacionais do prontuário (`pep-service`) e as requisições analíticas (`analytics-service`) quando o Kubernetes realiza o auto-escalonamento de pods.

* **Hipótese C (Compartilhamento de Infraestrutura OLTP e OLAP):**  
  O microsserviço de Analytics (processamento analítico - OLAP) está compartilhando a mesma instância do banco de dados relacional (OLTP) do módulo de Prontuário Eletrônico. A ausência de isolamento físico de infraestrutura faz com que consultas pesadas de relatórios interfiram na velocidade de gravação dos prontuários médicos.

* **Hipótese D (Throttling e Limites de Recursos de CPU no Cluster EKS):**  
  A elevação no uso de CPU nos pods do Kubernetes pode estar ativando o mecanismo de limitação de ciclos de processamento (*CPU Throttling*) imposto pelos parâmetros de `limits` configurados nos manifestos do Kubernetes, deixando a execução das requisições mais lenta nas instâncias do Spring Boot.

---

```
================================================================================
STATUS FINAL DO SISTEMA (SEXTA-FEIRA, 24/10/2026 - 23h45):
• Disponibilidade do Serviço: 100% no ar (Sem quedas ou erros HTTP 5xx).
• Latência Média de Resposta: 385ms (Degradada em relação ao baseline de 120ms).
• Alertas Críticos Disparados no PagerDuty: Nenhum alerta de indisponibilidade.
• Próxima Janela de Acompanhamento Programada: Segunda-feira, 26/10/2026, às 08h00.
================================================================================
```
