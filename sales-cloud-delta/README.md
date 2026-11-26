# 🚀 Implementação Salesforce Sales Cloud — Processo Comercial End-to-End

Implementação completa do processo comercial de uma empresa fictícia (**Delta Tecnologia**) na plataforma **Salesforce Sales Cloud**, cobrindo todo o ciclo de vendas: da captação do lead à geração da proposta, com automações, governança e previsão de receita.

> Projeto desenvolvido individualmente durante programa de formação de consultores, com entrega final apresentada em formato de **Sprint Review**.

---

## 📋 Sobre o projeto

A Delta Tecnologia precisava estruturar todo o seu processo comercial — que vivia espalhado em planilhas e e-mails — dentro de uma única plataforma. O objetivo foi arquitetar e desenvolver a solução no Salesforce, priorizando **automação, padronização e rastreabilidade** em cada etapa.

O trabalho foi dividido em cinco blocos, do cadastro do cliente até a apresentação da solução.

---

## 🛠️ Tecnologias e ferramentas

- **Salesforce Sales Cloud** (Developer Edition, Lightning Experience)
- **Apex** (Developer Console — inserção de quotas de forecast)
- **Fórmulas e Regras de Validação** do Salesforce
- **Processos de Aprovação** (Approval Processes)
- **Collaborative Forecasting**

---

## 🧩 O que foi implementado

### 1. Conta e Contato
- Campos customizados em **Conta**: Segmento, ERP atual, Nuvens contratadas (multi-seleção).
- Campos customizados em **Contato**: Cargo, Nível e Papel na compra.
- Listas relacionadas conectando Contas a Contatos, Oportunidades e Contratos — visão 360° do cliente em um só lugar.

### 2. Gestão de Leads com Qualificação Automática
- Campos de qualificação: Origem, Status, Motivos de desqualificação, caixas de seleção (Tomador de Decisão, Projeto Definido, Tem Orçamento) e Prazo de Decisão.
- **Campo fórmula de Score** que calcula a pontuação do lead automaticamente a partir dos critérios de qualificação.
- **Regra de validação** que controla o ciclo do lead: só permite converter leads qualificados (Score > 10) ou desqualificar os de baixa pontuação (Score ≤ 5).
- **Processo de conversão** mapeando os campos de qualificação para a Oportunidade.

### 3. Oportunidades, Funil de Vendas e Previsão
- **Caminho de Vendas (Path)** configurado por fase, com *Orientação para o Sucesso* e campos-chave guiando o vendedor em cada etapa.
- Fases: Discovery → Defesa Técnica → Proposta → Negociação → Fechado.
- **Collaborative Forecasting** trimestral, com hierarquia de dois níveis (gerente e executivo) e metas (quotas) por usuário.

### 4. Cotações e Governança de Desconto
- **Catálogo de produtos** representando os perfis profissionais (GP, Arquiteto, Desenvolvedor, Business Analyst) com tabela de preços por taxa/hora.
- Geração de cotações com cálculo automático de valores.
- **Processo de Aprovação de desconto**: descontos acima de 10% disparam aprovação do gerente; descontos menores seguem automaticamente — protegendo a margem sem travar o dia a dia do vendedor.
- Modelo de proposta configurado para geração de PDF.

### 5. Apresentação (Sprint Review)
- Demonstração ao vivo do fluxo completo, do lead à proposta.
- Material de apoio com identidade visual, roteiro e dados de demonstração.

---

## 💡 Principais decisões técnicas

- **Score como campo fórmula:** por ser calculado, não pode ser mapeado diretamente na conversão de Lead — foram criados campos-espelho na Oportunidade para preservar a pontuação.
- **Quotas via Apex:** no Developer Edition a edição inline de quota de forecast é limitada, então as metas foram inseridas via script Apex (`ForecastingQuota`) no Developer Console.
- **Governança por critério de entrada:** o processo de aprovação usa o critério "desconto > 10%", garantindo que apenas descontos relevantes exijam validação gerencial.

---

## 🎯 Resultado

Processo comercial 100% implantado e testado no Salesforce — padronizado, automatizado e com previsibilidade de receita. A solução entrega visibilidade total do funil, priorização automática de leads e controle de descontos, reduzindo trabalho manual e dando foco à equipe de vendas.

---

## 👩‍💻 Autora

**Maria Eduarda Cortellini**
Consultora Salesforce (Business Analyst) | Estudante de Ciência da Computação
