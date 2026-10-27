# 🔴 ESTUDO DE CASO COMPLETO: CENÁRIO 4
## **O Impasse do Comitê de Governança, Sabotagem Operacional e o Futuro do NexusCare**

---

# 📑 PARTE 1: CADERNO DO ALUNO E DO GRUPO

### ⚠️ Instruções para a Dinâmica em Grupo
Neste estudo de caso, a decisão **não será tomada por um único gestor individual**, mas sim pelo **Comitê de Crise e Governança Executiva de TI**.

Os alunos da turma deverão dividir-se em grupos (de 4 a 6 participantes). Cada grupo assumirá o papel do **Comitê Executivo de Crise da HealthTech Nexus**. Vocês deverão debater as divergências internas, filtrar as informações enganosas/imprecisas presentes nos relatórios técnicos, avaliar a pressão da alta gestão e votar, em conjunto, em qual proposta de intervenção o Comitê irá deliberar.

---

### 🏛️ 1. Contexto Geral e Histórico da Empresa
A **HealthTech Nexus** é o maior ecossistema SaaS de saúde digital da América Latina. Sua plataforma central, o **NexusCare v5.2**, gerencia o Prontuário Eletrônico do Paciente (PEP), agendas médicas, UTI, estoques de medicamentos e integrações de pronto-socorro para **85 grandes redes hospitalares privadas** (abrangendo mais de 420 hospitais físicos e 6 milhões de pacientes ativos).

* **Valuations e IPO:** A empresa está rigorosamente a **15 dias de formalizar seu IPO (Abertura de Capital) na B3**, avaliada em **R$ 1,2 bilhão**. A auditoria técnica internacional da *PricewaterhouseCoopers (PwC)* e o consórcio de bancos de investimento exigem um SLA comprovado de **99,99% de disponibilidade** nas últimas 72 horas para liberar a assinatura do prospecto definitivo.
* **Cultura da Empresa:** A organização vive um cabo de guerra histórico entre duas alas:
  1. *A Ala da Engenharia e Produto:* Defende inovação rápida, resiliência técnica, metodologias ágeis e arquitetura moderna de microsserviços.
  2. *A Ala de Governança e Finanças:* Defende contenção severa de custos, processos burocráticos engessados e a terceirização da infraestrutura para grandes consultorias legadas.

---

### 👥 2. Os Integrantes do Comitê de Crise e suas Agendas Políticas

#### 📊 Tabela de Cargos e Integrantes do Comitê Executivo

| Nome | Cargo / Função | Área de Atuação | Foco / Posição no Comitê de Crise |
| :--- | :--- | :--- | :--- |
| **Dr. Alberto Guimarães** | CEO & Presidente do Comitê | Presidência / Alta Gestão | Responde ao Conselho. Exige consenso e recuperação do SLA até as 06h00 para garantir o IPO. |
| **Ricardo Vance** | Gerente de Engenharia & SRE | Engenharia de Plataforma | Busca solução técnica baseada em dados de observabilidade e protege a equipe de intervenções imaturas. |
| **Marcos Viana** | Diretor de Governança e Risco | Governança (Ex-CIO) | Antagonista. Força a terceirização para a consultoria *LegacyTech* (da qual é conselheiro remunerado). |
| **Juliana Siqueira** | CFO (Chief Financial Officer) | Finanças & Controladoria | Foco na contenção do caixa. Veta estouros de custos na AWS para não prejudicar o EBITDA pré-IPO. |
| **Dra. Helena Ramos** | CMO (Chief Medical Officer) | Diretoria Médica | Representa os hospitais e pronto-socorros. Exige o sistema no ar para evitar risco de morte aos pacientes. |
| **Camila Torres** | Tech Lead SRE | Operações / Infraestrutura | Voz da equipe técnica. Alerta sobre risco de *burnout* e demissão em massa em caso de decisões arbitrárias. |
| **Victor Hugo** | Lead SecOps & Cyber | Cibersegurança & Compliance | Focado em compliance e invasões. Suspeita de ataque DDoS/exfiltração e sugere notificação legal. |

---

#### 📝 Detalhamento dos Perfis e Agendas Políticas

1. **Dr. Alberto Guimarães (CEO / Presidente do Comitê):**
   * *Perfil:* Visionário, pragmático e implacável. Responde diretamente ao Conselho de Administração e aos fundos de *Venture Capital*.
   * *Objetivo:* Garantir o IPO a qualquer custo. Não tolera jargões técnicos e exige consenso do Comitê. Mandou o recado: *"Se o Comitê tomar a decisão errada e os hospitais continuarem parados até as 06h00, todos os executivos desta sala serão destituídos pelo Conselho por infração fiduciária, e a equipe técnica será demitida em massa."*

2. **Ricardo Vance (Gerente de Engenharia de Plataforma e SRE):**
   * *Perfil:* Defensor do time técnico e da engenharia moderna. Experiente, porém sobrecarregado pela pressão política das reuniões de diretoria.
   * *Objetivo:* Salvar a plataforma com embasamento em dados de observabilidade, evitando que soluções milagrosas impostas por não-técnicos destruam o banco de dados.

3. **Marcos Viana (Diretor de Governança, Risco e Compliance - O Antagonista):**
   * *Perfil:* Ex-CIO da empresa que foi rebaixado/remanejado para a área de Governança após a reestruturação da TI. Nutre forte ressentimento em relação à atual equipe de Engenharia.
   * *Agenda Oculta:* É conselheiro remunerado de uma grande consultoria internacional de TI legada (*LegacyTech Solutions*). Quer provar a incapacidade do time interno para forçar a terceirização de todo o departamento de TI para a sua consultoria, garantindo um contrato milionário e retomando o controle operacional da empresa.

4. **Juliana Siqueira (CFO - Chief Financial Officer):**
   * *Perfil:* Rígida guardiã do caixa. Cortou 15% do orçamento de Nuvem no trimestre anterior e aplica veto financeiro a qualquer gasto não previsto.
   * *Objetivo:* Impedir qualquer explosão de custos operacionais na Nuvem AWS/Azure que possa estourar o EBITDA do balanço pré-IPO.

5. **Dra. Helena Ramos (Diretora Médica / CMO - Chief Medical Officer):**
   * *Perfil:* Médica intensivista. Representa a voz dos clientes (hospitais, médicos e diretores clínicos).
   * *Objetivo:* Não se importa com custos de nuvem ou IPO. Sua prioridade absoluta é fazer as telas dos computadores dos prontos-socorros voltarem a funcionar, pois pacientes em estado grave dependem da liberação de prescrições e leitos.

6. **Camila Torres (Tech Lead SRE / Representante do Time Operacional):**
   * *Perfil:* Engenheira brilhante, referência técnica da equipe de 6 analistas de SRE/DevOps.
   * *Situação:* À beira do *burnout* devido a plantões seguidos. Alerta que o time se demitirá em massa se o Comitê tomar uma decisão arbitrária que destrua o trabalho da engenharia ou jogue a culpa da crise nas costas dos desenvolvedores.

---

### 📜 3. O Histórico do Time: Vitórias da Engenharia e Cicatrizes do Passado

* **A Grande Vitória do Passado (A Virada da Pandemia):** Durante a primeira onda de acessos massivos em telemedicina na pandemia, o time de SRE (liderado por Ricardo e Camila) trabalhou durante 72 horas ininterruptas e rearquiteto o sistema para microsserviços. A plataforma suportou um aumento de 300% no tráfego sem nenhuma queda por 90 dias consecutivos, garantindo o crescimento que permitiu à empresa projetar o IPO atual.
* **A Cicatriz do Passado (O Incidente do Faturamento de 2024):** Há seis meses, em um *deploy* de emergência feito na sexta-feira à noite sem mecanismo de *rollback* automático, uma alteração em um parâmetro do banco de dados causou a perda de sincronia do módulo de bilhetagem hospitalar por 14 horas. A empresa perdeu R$ 1,8 milhão em faturamento direto e sofreu auditoria severa comandada por Marcos Viana, que desde então usa esse evento para desqualificar o time interno.

---

### 🚨 4. A Situação Crítica em Tempo Real

É **domingo, 22h15**. Faltam menos de **8 horas para a abertura dos mercados financeiros e o início do turno matutino dos hospitais**. 

O painel de monitoramento do Datadog e Grafana disparou alerta vermelho geral: a plataforma **NexusCare v5.2** sofreu uma degradação severa catastrófica. O tempo médio de latência para consulta de prontuários e prescrição de medicamentos saltou de **120ms para 34,5 segundos**.

* **Impacto Operacional:** **52 dos 85 grupos hospitalares parceiros** pararam totalmente o atendimento em suas emergências. Ambulâncias estão sendo desviadas de prontos-socorros, exames de tomografia não conseguem ser laudados e médicos estão anotando prescrições em papel de forma improvisada.
* **Pressão Externa:** Jornalistas de portais de notícias de saúde começaram a publicar matérias sobre "apagão digital nos hospitais privados". Três diretores de fundos de investimento ligaram para o CEO exigindo esclarecimentos até as 06h00 da manhã.

O CEO Dr. Alberto abriu a sessão extraordinária do **Comitê de Crise e Governança** e exigiu que o grupo apresente uma **deliberação votada e consensual até as 23h30**.

---

### 📊 5. Os Dossiês e Relatórios Técnicos Divergentes Apresentados ao Comitê

#### 📄 DOSSIÊ 1: O Relatório da Consultoria Externa *LegacyTech* (Apresentado por Marcos Viana)
> *"Nossos auditores seniores revisaram a infraestrutura e identificaram que o banco de dados PostgreSQL 14 atingiu o limite crítico de bloqueios internos (Ghost Locks) acima de 10 GB de massa de dados. A única solução técnica viável para impedir a perda irreversível de dados é autorizar o comando `VACUUM FULL` emergencial com eliminação da árvore de índices em produção e reboote dos nós primários. Além disso, informamos que qualquer tentativa de isolar ou desligar temporariamente as APIs de integração por mais de 4 horas violará o Artigo 15 da LGPD, gerando uma multa direta e automática de 5% do faturamento global bruto da HealthTech Nexus aplicável pela ANPD nas primeiras horas da manhã."*

#### 📄 DOSSIÊ 2: O Alerta Financeiro da Diretoria Executiva (Apresentado pela CFO Juliana Siqueira)
> *"Nossos custos de infraestrutura AWS já estouraram em 18% este mês. Se o Comitê autorizar o acionamento do ambiente de contingência e Failover para o AWS Aurora Serverless v2 em modo de capacidade máxima Multirregião, os custos computacionais por hora saltarão de R$ 1.200 para R$ 85.000 por hora. Isso queimará R$ 2,5 milhões do nosso caixa até o final da semana, estourará o EBITDA do balanço auditado e cancelará a precificação das ações do IPO na B3 pela auditoria da PwC."*

#### 📄 DOSSIÊ 3: A Análise de Telemetria do Kubernetes (Apresentada por Gustavo, Engenheiro DevOps)
> *"Os pods dos microsserviços de Prontuário (`pep-service`) estão apresentando consumo de 100% de CPU no cluster de Kubernetes (EKS). O comportamento indica claramente um Memory Leak (vazamento de memória) grave no código Java introduzido no deploy de sexta-feira. A única forma de manter o serviço no ar é escalar horizontalmente o cluster, aumentando a quantidade de instâncias de pods de 20 para 100 réplicas imediatamente."*

#### 📄 DOSSIÊ 4: O Diagnóstico de Cibersegurança (Apresentado por Victor Hugo, SecOps)
> *"Registramos um volume anómalo de tráfego vindo de endereços IP da Europa do Leste direcionados aos endpoints da API secundária de relatórios históricos (`/api/v2/analytics`). Suspeitamos de um ataque de Negação de Serviço Distribuído (DDoS) ou tentativa de exfiltração de dados. Recomendamos o desligamento total do acesso externo da aplicação e o acionamento do protocolo de contenção de incidentes cibernéticos com notificação à Polícia Federal."*

---

### 🎲 6. As Propostas de Deliberação para Votação do Comitê

#### 🔹 PROPOSTA DE DELIBERAÇÃO A (Aprovada pela Governança e Consultoria Externa)
1. Atender integralmente à orientação da consultoria *LegacyTech* e do Diretor Marcos Viana, executando o comando `VACUUM FULL` no banco de dados principal de produção e destruindo a árvore de índices para recriá-la do zero.
2. Autorizar o escalonamento horizontal no Kubernetes multiplicando as instâncias de pods por 5x (para 100 pods) para absorver o suposto *Memory Leak*.
3. Assinar o contrato emergencial de R$ 450.000 para que a consultoria *LegacyTech* assuma a gestão da infraestrutura a partir desta madrugada.

#### 🔹 PROPOSTA DE DELIBERAÇÃO B (Aprovada pela Engenharia de Plataforma e SRE)
1. Rejeitar formalmente a intervenção do `VACUUM FULL` e o diagnóstico da consultoria externa.
2. Executar um isolamento lógico e estancamento de crise direcionado:
   a) Identificar e encerrar (*kill*) via script atômico as conexões de consultas lentas (*long-running queries*) bloqueando a tabela principal do banco de dados.
   b) Aplicar um *Hotfix* de emergência no código para desativar a rotina da API secundária de relatórios (`/api/v2/analytics`), que foi implantada na sexta-feira sem índice adequado e está causando engarrafamento de consultas no PostgreSQL.
   c) Habilitar a camada de contingência e cache local no Redis para que os hospitais continuem consultando e gravando prontuários em modo de degradação graciosa (*read-only/cache*) sem derrubar o banco principal.
   d) Manter o plano de escalonamento dentro do orçamento aprovado, sem acionar custos de destruição financeira na AWS.

#### 🔹 PROPOSTA DE DELIBERAÇÃO C (Aprovada pelo Comitê Financeiro e de Segurança)
1. Executar o *Failover* completo e imediato de desastre para a Nuvem Secundária em Microsoft Azure (Região Leste dos EUA), desligando totalmente o ambiente AWS para estancar o suposto ataque de DDoS e o estouro de custos serverless.
2. Congelar o código-fonte atual e contratar uma auditoria externa de forense digital e segurança por R$ 600.000 para isolar os logs e procurar indícios de vazamento de dados durante as próximas 48 horas.
3. Notificar o consórcio do IPO sobre uma janela de manutenção programada de contingência para reconfigurar a rede entre provedores.

#### 🔹 PROPOSTA DE DELIBERAÇÃO D (Aprovada pela Diretoria Médica e Operações)
1. Declarar *Blackout* operacional programado e colocar a plataforma inteira em modo de manutenção estrita até as 08h00 da manhã de segunda-feira.
2. Determinar formalmente que os 52 grupos hospitalares paralisem o uso do sistema digital e ativem o protocolo analógico de contingência médica (uso de papel, carimbos e pranchas manuais nos prontos-socorros) pelas próximas 24 horas.
3. Aprovar a contratação de uma nova arquitetura corporativa completa para substituir o software nos próximos 6 meses, mantendo o sistema atual congelado para evitar novos incidentes até a conclusão do IPO.

---

