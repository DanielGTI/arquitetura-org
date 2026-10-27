# 🏥 HealthTech Nexus S.A. — Documentação de Arquitetura, Operações e Governança (2026)

[![Arquitetura SaaS](https://img.shields.io/badge/Architecture-AWS%20%7C%20EKS%20%7C%20PostgreSQL-blue)](#-vis%C3%A3o-geral-da-empresa-e-do-cen%C3%A1rio)
[![Status do Sistema](https://img.shields.io/badge/Status-100%25%20Uptime%20(Degradado)-yellow)](#-situa%C3%A7%C3%A3o-operacional-da-release-v52)
[![IPO Ready](https://img.shields.io/badge/IPO-B3%20Novo%20Mercado%202027-green)](#-relat%C3%B3rios-dispon%C3%ADveis-no-reposit%C3%B3rio)

Este repositório contém a suíte completa de relatórios técnicos, financeiros, regulatórios e executivos da **HealthTech Nexus Soluções em Saúde S.A.** referentes ao exercício de 2026, com foco na arquitetura da plataforma **NexusCare v5.2**, governança corporativa e preparação para o IPO em 2027.

---

## 🏢 Visão Geral da Empresa e do Cenário

A **HealthTech Nexus S.A.** é provedora da principal plataforma SaaS *multi-tenant* de gestão hospitalar, prontuário eletrônico (PEP) e inteligência médica do Brasil.

* **Base de Clientes:** 85 grandes redes hospitalares privadas (423 hospitais físicos e 185 clínicas especializadas).
* **Escala Operacional:** 6 milhões de prontuários ativos de pacientes e mais de 68.000 profissionais de saúde ativos por dia (DAU).
* **Desempenho Financeiro:** ARR de R$ 184,5 milhões com margem EBITDA de 27,0% e NRR de 124,5%.
* **Infraestrutura:** Cloud Native AWS (Region `us-east-1`), orquestrada em Kubernetes EKS v1.28 com AWS Aurora PostgreSQL 14.9, Redis Enterprise e Apache Kafka (MSK).

---

## ⚡ Situação Operacional da Release v5.2

Na noite de **24 de Outubro de 2026 (Sexta-feira)**, foi implantada a versão **Release Candidate v5.2**, que introduziu a nova **API v2 de Analytics** (`analytics-service`) para disponibilizar dashboards executivos em tempo real para as diretorias clínicas.

### Resumo do Evento Registrado:
1. **Disponibilidade (Uptime):** $100\%$ mantido (zero erros de sistema HTTP 5xx).
2. **Desempenho (Latência p95):** Elevou-se de $120\text{ms}$ (baseline) para $385\text{ms}$ pós-deploy.
3. **Causa-Raiz Investigada:** A consulta agregadora de analytics utiliza uma varredura sequencial (`Seq Scan`) no PostgreSQL devido à falta de um índice composto `(tenant_id, created_at)` na tabela de prontuários (`patient_records`, 4,8 TB).
4. **Efeito Cascata:** O aumento de tempo no banco represou o pool de conexões do PgBouncer, elevando o uso de CPU dos pods Java no EKS e disparando o auto-escalonamento de réplicas pelo HPA (de 12 para 20 pods).

---

## 🗺️ Arquitetura Resumida do Sistema

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           EDGE / KONG GATEWAY                               │
│              AWS CloudFront ──► WAF ──► Kong Enterprise Gateway             │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                       KUBERNETES EKS CLUSTER (v1.28)                        │
│ ┌───────────────────────┐ ┌───────────────────────┐ ┌─────────────────────┐ │
│ │     pep-service       │ │  prescription-service │ │ integration-service │ │
│ │  (Java 17 / Spring)   │ │  (Assinatura Digital) │ │ (HL7 / FHIR Router) │ │
│ └───────────┬───────────┘ └───────────┬───────────┘ └──────────┬──────────┘ │
│             │                         │                        │            │
│             └─────────────────────────┼────────────────────────┘            │
│                                       ▼                                     │
│                  ┌────────────────────────────────────────┐                 │
│                  │           analytics-service            │                 │
│                  │        (Módulo de BI - Novo v5.2)      │                 │
│                  └────────────────────┬───────────────────┘                 │
└───────────────────────────────────────┼─────────────────────────────────────┘
                                        │
             ┌──────────────────────────┼──────────────────────────┐
             ▼                          ▼                          ▼
┌──────────────────────────┐┌──────────────────────────┐┌──────────────────────────┐
│   KAFKA (AWS MSK v3.5)   ││   REDIS CLUSTER v7.0     ││ AURORA POSTGRESQL 14.9   │
│    (Mensageria Async)    ││   (Cache em Memória)     ││ (PgBouncer Pooler 500)   │
└──────────────────────────┘└──────────────────────────┘└──────────────────────────┘
```

---

## 📁 Relatórios Disponíveis no Repositório

Todos os relatórios detalhados do ecossistema NexusCare estão publicados e vinculados abaixo:

| Documento | Descrição e Escopo | Link Direto |
| :--- | :--- | :--- |
| **Relatório Técnico de Infraestrutura** | Análise detalhada da arquitetura do sistema, telemetria do deploy da v5.2, plano de execução SQL e diagnóstico de concorrência. | [📄 relatorio_tecnico.md](https://github.com/DanielGTI/arquitetura-org/blob/main/relatorio_tecnico.md) |
| **Relatório Financeiro & IPO** | Métricas financeiras ($ARR$, $EBITDA$, $LTV/CAC$), FinOps de nuvem AWS, valuation prévio e cronograma de IPO na B3/SEC. | [📊 relatorio_financeiro_ipo.md](https://github.com/DanielGTI/arquitetura-org/blob/main/relatorio_financeiro_ipo.md) |
| **Relatório de Uso e Clientes** | Métricas de adoção nos clientes (Tiers 1, 2 e 3), volume diário de requisições, notas de CSAT e chamados abertos por hospital. | [🏥 relatorio_uso_clientes.md](https://github.com/DanielGTI/arquitetura-org/blob/main/relatorio_uso_clientes.md) |
| **Relatório de Governança e Contratos** | Matriz de compliance LGPD/HIPAA, regras contratuais de SLA de latência, cláusulas de penalidade e gestão de risco de terceiros. | [📜 relatorio_governanca_contratos.md](https://github.com/DanielGTI/arquitetura-org/blob/main/relatorio_governanca_contratos.md) |
| **Relatório de Cibersegurança** | Balanço de segurança dos últimos 6 meses, ataques DDoS/SQLi barrados pelo SOC, resultados de Pen-Test e DevSecOps. | [🛡️ relatorio_ciberseguranca.md](https://github.com/DanielGTI/arquitetura-org/blob/main/relatorio_ciberseguranca.md) |
| **Relatório Consolidado da Alta Diretoria** | Parecer geral do CEO e Conselho de Administração unificando todas as áreas para o fechamento de 2026. | [🏛️ relatorio_alta_diretoria_2026.md](https://github.com/DanielGTI/arquitetura-org/blob/main/relatorio_alta_diretoria_2026.md) |

---

## 🛠️ Como Contribuir e Executar Alterações

1. **Ajustes na Base de Dados:** Qualquer script de migração SQL para o PostgreSQL deve obrigatoriamente passar por aprovação prévia do Comitê de Governança e DBA (CAB), acompanhado de `EXPLAIN ANALYZE`.
2. **Propostas de Mudança em Infraestrutura (IaC):** Alterações nos manifestos do Kubernetes ou Terraform AWS devem ser submetidas via Pull Request neste repositório com tag `infrastructure/aws`.

---

**HealthTech Nexus Soluções em Saúde S.A.**  
*Engenharia de Plataforma, NOC & Governança Corporativa — 2026*
