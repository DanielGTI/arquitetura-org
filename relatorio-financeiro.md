# 📊 RELATÓRIO FINANCEIRO E CONSULTORIA PREPARATÓRIA PARA IPO — 2026

**EMISSOR:** Diretoria Financeira (CFO) & Consultoria de M&A e IPO (Morgan Stanley / BTG Pactual)  
**EMPRESA:** HealthTech Nexus Soluções em Saúde S.A.  
**DATA DE EMISSÃO:** Outubro de 2026  
**CLASSIFICAÇÃO:** Confidencial / ESTRITAMENTE RESTRITO  

---

## 1. RESUMO EXECUTIVO FINANCEIRO (YTD 2026)

A **HealthTech Nexus S.A.** encerra o 3º trimestre de 2026 em posição financeira altamente sólida, sustentada pelo modelo de receita recorrente SaaS (*Software as a Service*) *multi-tenant* focado no mercado B2B de saúde de alta complexidade.

### Principais Indicadores Financeiros (KPIs):
* **Receita Recorrente Anualizada (ARR):** $\text{R\$ } 184,5 \text{ milhões}$ (crescimento de $38,2\%$ YoY).
* **Receita Recorrente Mensal (MRR):** $\text{R\$ } 15,375 \text{ milhões}$.
* **Margem Bruta:** $76,4\%$.
* **EBITDA Ajustado:** $\text{R\$ } 49,8 \text{ milhões}$ (Margem EBITDA de $27,0\%$).
* **Net Retention Rate (NRR):** $124,5\%$ (indicando forte expansão da base existente via módulos como o `analytics-service`).
* **Custo de Aquisição de Cliente (CAC):** $\text{R\$ } 142.000,00$.
* **Lifetime Value (LTV):** $\text{R\$ } 2.450.000,00$.
* **Razão LTV / CAC:**
  $$LTV/CAC = \frac{\text{R\$ } 2.450.000}{\text{R\$ } 142.000} \approx 17,25x$$
  *(Reflete altíssima eficiência de vendas e retenção contínua).*

---

## 2. ESTRUTURA DE OPEX DE INFRAESTRUTURA E TECNOLOGIA

O custo de processamento por prontuário ativo é o principal vetor do Custo dos Produtos Vendidos (COGS). A eficiência da infraestrutura AWS impacta diretamente as margens operacionais.

### Custo Mensal de Nuvem (AWS Baseline):
* **AWS EKS & Compute (EC2 `m6i.2xlarge`):** $\text{R\$ } 420.000,00 / \text{mês}$.
* **AWS Aurora PostgreSQL (`db.r6g.4xlarge`):** $\text{R\$ } 310.000,00 / \text{mês}$.
* **AWS MSK (Kafka) & Redis Cluster:** $\text{R\$ } 115.000,00 / \text{mês}$.
* **Borda, WAF & CDN (CloudFront/Kong):** $\text{R\$ } 65.000,00 / \text{mês}$.
* **Ferramentas de Observabilidade (Datadog/Logstash):** $\text{R\$ } 140.000,00 / \text{mês}$.
* **Custo Total de Infra/Nuvem Mensal:** $\text{R\$ } 1.050.000,00$.

> **Nota de Risco Financeiro sobre a Release v5.2:**  
> O acionamento do escalonamento automático de pods (*HPA*) no cluster EKS durante a degradação da v5.2 elevou a contagem de nós computacionais em $66\%$, gerando um custo incremental estimado em $\text{R\$ } 18.500,00/dia$ caso o comportamento ineficiente das consultas no PostgreSQL persista sem correção de indexação.

---

## 3. VALUATION PRELIMINAR E MODELAGEM DE GOING-PUBLIC

A avaliação prévia (*Pre-Money Valuation*) elaborada pela consultoria financeira projeta a Nexus S.A. na faixa de múltiplos praticados por SaaS HealthTechs globais (ex: Veeva Systems, Doximity):

### Múltiplos de Valuation Aplicados:
* **Múltiplo de ARR Conservador:** $8,0x \implies \text{Valuation: R\$ } 1,476 \text{ bilhão}$.
* **Múltiplo de ARR Base (Target):** $11,5x \implies \text{Valuation: R\$ } 2,121 \text{ bilhões}$.
* **Múltiplo de ARR Otimista:** $14,0x \implies \text{Valuation: R\$ } 2,583 \text{ bilhões}$.

---

## 4. AUDITORIA E PRONTIDÃO PARA O IPO (ROADMAP 2027)

| Frente de Trabalho | Status | Responsável | Observações |
| :--- | :--- | :--- | :--- |
| **Auditoria CVM / Big Four** | 🟢 Concluído | PwC | Demonstrações contábeis de 2024, 2025 e 2026 sem ressalvas. |
| **Sistemas Internos de SOX** | 🟡 Em Andamento | EY | Implementação de controles internos de TI (ITGC). |
| **Comitê de Auditoria Independente** | 🟢 Formado | Conselho | 3 membros independentes empossados em Q3-2026. |
| **Adequação Contratual de SLA** | 🔴 Requer Atenção | Jurídico | Risco de multas por degradação em contas Tier-1. |

---

## 5. RECOMENDAÇÕES DA CONSULTORIA FINANCEIRA

1. **Eficiência de COGS (Engenharia Financeira de FinOps):** Sanar imediatamente a ineficiência de CPU no banco de dados trazida na Release v5.2 para evitar erosão de $1,8\%$ na margem bruta do Q4.
2. **Captação Alvo (Offering Size):** Emissão primária projetada em $\text{R\$ } 400 \text{ milhões}$ ($20\%$ de *free float*), destinada à expansão internacional (América Latina) e fusões/aquisições (M&A).
3. **Janela Pré-IPO Target:** Maio de 2027 na B3 (Novo Mercado), com pedido de registro simultâneo via SEC (Rule 144A/Req S) para investidores institucionais estrangeiros.
