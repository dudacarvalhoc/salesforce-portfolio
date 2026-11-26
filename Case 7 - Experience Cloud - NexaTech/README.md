# 🤝 NexaTech Partner Portal: Salesforce Experience Cloud
 
> Portal de parceiros comerciais construído no **Salesforce Experience Cloud** para a empresa fictícia **NexaTech Soluções**.
> Case 7 do Programa de Formação de Consultores (Agoge) · Grupo Taak 🚀
 
![Salesforce](https://img.shields.io/badge/Salesforce-Experience%20Cloud-00A1E0?logo=salesforce&logoColor=white)
![Flow](https://img.shields.io/badge/Automação-Screen%20Flow-1B4F8C)
![Status](https://img.shields.io/badge/status-concluído-53BE80)
 
---
 
## 📌 Sumário
 
- [O desafio](#-o-desafio)
- [A solução](#-a-solução)
- [Segurança e visibilidade](#-segurança-e-visibilidade)
- [Objeto Sugestão de Produto/Serviço](#-objeto-sugestão-de-produtoserviço)
- [Screen Flow: Cadastro de Sugestão](#-screen-flow-cadastro-de-sugestão)
- [O site](#-o-site)
- [Relatórios e dashboard](#-relatórios-e-dashboard)
- [Usuários de teste](#-usuários-de-teste)
- [Decisões técnicas](#-decisões-técnicas)
- [Limitações e próximos passos](#-limitações-e-próximos-passos)
- [Como testar](#-como-testar)
---
 
## 🎯 O desafio
 
A NexaTech vende tecnologia por meio de **representantes comerciais externos** (os parceiros). Eles não tinham um canal próprio:
 
- 😕 **Sem autonomia:** dependiam do time interno para registrar leads e acompanhar negócios
- 🔒 **Sem privacidade:** cada parceiro só pode ver os próprios dados, nunca os de outro parceiro
- 💡 **Sem canal de ideias:** sugestões de novos produtos e serviços chegavam soltas, sem registro nem status
**Objetivo:** um portal com a marca NexaTech, seguro por padrão, onde o parceiro trabalha sozinho.
 
---
 
## 🧩 A solução
 
Seis entregas no Experience Cloud, cobrindo as cinco user stories do case:
 
| # | Entrega | O que foi feito |
|---|---|---|
| 01 | 🏗️ **Configuração base** | Site **Partner Central** em `/parceiros`, com logo, cores, cabeçalho e rodapé da marca |
| 02 | 🔐 **Segurança** | OWD **Private** + perfil clonado com licença **Partner Community** |
| 03 | 🌐 **Páginas públicas** | Login customizado e página **Sobre a Empresa** acessível sem login |
| 04 | 🏠 **Portal do parceiro** | Home com dashboard, Leads, Oportunidades e Sua Conta |
| 05 | 📦 **Objeto Sugestão** | Objeto custom com Record Types **Produto** e **Serviço** |
| 06 | ⚡ **Screen Flow** | Cadastro de Produto, Serviço ou **Ambos**, com a conta preenchida automaticamente |
 
### 🗺️ Jornada do parceiro
 
```
🌐 Página pública → 🔑 Login → 🏠 Home (dashboard) → 📈 Leads e Oportunidades → 💡 Sugestões
```
 
---
 
## 🔐 Segurança e visibilidade
 
A segurança foi pensada em **quatro camadas**:
 
| Camada | Configuração | Por quê |
|---|---|---|
| 🎫 **Licença** | Partner Community | Dá acesso nativo a Leads e Oportunidades no portal |
| 👤 **Perfil** | `Parceiro Comercial Portal` (clone do Partner Community User) | Define o que o parceiro pode fazer, sem alterar o perfil padrão |
| 🧱 **OWD** | Lead, Opportunity e Account **Private** (interno e externo) | Por padrão, ninguém vê registro de ninguém |
| 🏷️ **Ownership** | Cada parceiro é dono dos seus registros | Junto com o OWD Private, cada um vê só o que é seu |
 
### Permissões do perfil
 
| Objeto | Ler | Criar | Editar | Excluir | Observação |
|---|:-:|:-:|:-:|:-:|---|
| Lead | ✅ | ✅ | ✅ | ❌ | + permissão **Convert Leads** |
| Opportunity | ✅ | ✅ | ✅ | ❌ | |
| Account | ✅ | ✅ | ❌ | ❌ | Create só por causa da conversão de lead; na tela é somente leitura |
| Contact | ✅ | ✅ | ❌ | ❌ | A conversão de lead cria um contato |
| Sugestão de Produto/Serviço | ✅ | ✅ | ❌ | ❌ | Criada pelo Flow |
 
Outras configurações do perfil:
- ✅ **Run Reports**, para o dashboard da Home
- ✅ FLS liberado nos campos usados no portal
- ✅ Record Types Produto e Serviço atribuídos
- ✅ Acesso ao Screen Flow restrito a este perfil
### Layouts do parceiro
 
| Layout | Campos | Ações |
|---|---|---|
| **Lead Parceiro** | Nome, Empresa, Status, Origem, Data de Criação | Edit · Convert · Clone |
| **Opp Parceiro** | Nome, Valor, Fase, Data de Fechamento, Conta | Edit · Clone |
| **Account Parceiro** | Dados da conta | Nenhuma (somente leitura) 👀 |
 
> 💬 **Resumo:** a licença dá o acesso, o perfil define o que ele pode fazer, e o OWD Private garante que ele veja só o que é dele.
 
---
 
## 📦 Objeto Sugestão de Produto/Serviço
 
Objeto custom `Sugestao_de_Produto_Servico__c` para os parceiros enviarem ideias à NexaTech.
 
### Campos
 
| Campo | Tipo | Detalhes |
|---|---|---|
| Número da Sugestão | Auto Number | `SUG-{0000}` |
| Descrição | Long Text Area | |
| Conta | Lookup (Account) | Preenchida pelo Flow |
| Status | Picklist | Pendente *(padrão)*, Em Análise, Aprovada, Recusada |
| Categoria do Produto | Picklist | Software, Hardware, Acessório, Licença / Assinatura, Outro |
| Características do Produto | Long Text Area | |
| Preço Estimado | Currency | |
| Tipo de Serviço | Picklist | Consultoria, Implantação, Suporte Técnico, Treinamento, Outro |
| Escopo do Serviço | Long Text Area | |
| Frequência | Picklist | Única, Mensal, Anual |
 
### Record Types e layouts
 
| Record Type | Layout | Obrigatórios no layout |
|---|---|---|
| 📦 **Produto** | Layout Produto | Descrição, Categoria, Características |
| 🛠️ **Serviço** (`Servico`) | Layout Serviço | Descrição, Tipo de Serviço, Escopo |
 
> 💡 A obrigatoriedade fica **no layout**, não no campo, para cada tipo pedir só o que faz sentido.
 
Também tem: list view **Minhas Sugestões** e histórico do campo Status.
 
---
 
## ⚡ Screen Flow: Cadastro de Sugestão
 
```
Start
 ├─ 🔍 Buscar Usuario        → usuário logado (AccountId)
 ├─ 🔍 Buscar RT Produto     → Record Type pelo DeveloperName
 ├─ 🔍 Buscar RT Servico     → Record Type pelo DeveloperName
 ├─ 🖥️ Tela Tipo             → Produto / Serviço / Ambos
 ├─ 🖥️ Tela Formulario       → seções com visibilidade condicional
 ├─ 🔀 Qual Tipo
 │    ├─ Produto → ➕ Criar Produto
 │    ├─ Servico → ➕ Criar Servico
 │    └─ Ambos   → ➕ Criar Produto Ambos → ➕ Criar Servico Ambos
 └─ ✅ Tela Sucesso
```
 
**Regras garantidas:**
- 🏢 **Conta automática:** vem do usuário logado, o parceiro nunca escolhe
- ⏳ **Status = Pendente** em toda sugestão nova
- 🆔 **Sem ID fixo:** Record Types buscados pelo DeveloperName
- 👤 **Contexto do usuário:** o Flow respeita as permissões do parceiro
- 🔒 **Acesso restrito** ao perfil Parceiro Comercial Portal
---
 
## 🌐 O site
 
**Template:** Partner Central · **URL:** `/parceiros`
 
| Página | Acesso | Conteúdo |
|---|---|---|
| 🔑 **Login** | Público | Logo NexaTech, cores da marca |
| 🏢 **Sobre a Empresa** (`/s/sobre`) | Público | Banner, história, números, links e botão "Entrar no Portal" |
| 🏠 **Home** | Logado | Boas-vindas, banner, dashboard do parceiro e Lead Inbox |
| 📈 **Leads** | Logado | Lista e detalhe dos leads do parceiro |
| 💰 **Oportunidades** | Logado | Lista e detalhe das oportunidades |
| 🏢 **Sua Conta** | Logado | Conta do parceiro, somente leitura |
| 💡 **Sugestões** | Logado | Screen Flow + lista Minhas Sugestões |
 
**Menu:** Home · Leads · Oportunidades · Sua Conta · Sugestões
 
**Branding 🎨**
- Cores: azul `#1B4F8C` (marca) e `#2E86DE` (links)
- Logo NexaTech no cabeçalho e na tela de login
- Rodapé com nome, contato e copyright em todas as páginas
---
 
## 📊 Relatórios e dashboard
 
| Item | Configuração |
|---|---|
| 📄 **Meus Leads** | Filtro *My leads*, agrupado por Lead Status |
| 📄 **Minhas Oportunidades** | Filtro *My opportunities*, agrupado por Stage |
| 📊 **Painel do Parceiro** | Dashboard **dinâmico** (*View as: the dashboard viewer*) |
| 📁 **Pasta Portal Parceiros** | Compartilhada com os usuários parceiros |
 
> 💡 Relatório não aceita `{!$User.Id}` como filtro, então o "por parceiro" vem do filtro **"My"** + dashboard dinâmico: cada um abre o mesmo painel e vê só os próprios números.
 
---
 
## 👥 Usuários de teste
 
| Conta parceira | Usuário | Dados |
|---|---|---|
| 🅰️ **Parceiro A** | Ana Silva | 3 leads · 1 oportunidade |
| 🅱️ **Parceiro B** | Bruno Costa | 2 leads · 1 oportunidade |
 
✅ **Testado:** a Ana não vê nada do Bruno, e o Bruno não vê nada da Ana.
 
---
 
## 🧠 Decisões técnicas
 
| Decisão | Motivo |
|---|---|
| Account com **Read + Create**, sem Edit | Converter lead cria conta e contato; sem Edit, a conta segue somente leitura |
| Dashboard com relatórios **"My"** + visualizador dinâmico | É o filtro nativo por usuário logado |
| AccountId via **Get Records em User** | Mais confiável que `$User.AccountId`, mesmo resultado |
| Record Types pelo **DeveloperName** | Nenhum ID fixo no Flow: funciona em qualquer org |
| **Layouts próprios** para o parceiro | Sem botões de excluir, trocar dono ou compartilhar |
| **Sharing Sets não usados** | Valem para licenças de cliente, não de parceiro |
 
---
 
## 🚧 Limitações e próximos passos
 
- ⚠️ Na org de desenvolvimento, os dois parceiros ficaram no mesmo partner role. O isolamento se mantém pelo OWD Private, e os relatórios foram compartilhados **por usuário**, nunca por papel.
- 🔜 **Num projeto real:**
  - um partner role por conta parceira
  - um Public Group "Parceiros" para compartilhar pastas
  - Audiences por perfil nas páginas
  - processo de aprovação das sugestões com notificação ao parceiro
---
 
## 🧪 Como testar
 
1. 🌐 **Visitante:** janela anônima → `/parceiros/s/sobre` abre sem login → "Entrar no Portal" leva ao login
2. 🔑 **Ana:** contato Ana Silva → *Log in to Experience as User*
3. 🏠 **Home:** dashboard mostra só os dados da Ana
4. 📈 **Leads:** só os leads dela → editar status → **converter** (cria conta, contato e oportunidade)
5. 💰 **Oportunidades:** criar uma nova
6. 🏢 **Sua Conta:** Parceiro A sem botão Editar
7. 💡 **Sugestões:** enviar "Ambos" → 2 registros com **Conta = Parceiro A** e **Status = Pendente**
8. 🅱️ **Bruno:** logar como Bruno → **nada da Ana aparece** ✅
---
 
## 🛠️ Tecnologias
 
`Salesforce Experience Cloud` · `Partner Central` · `Screen Flow` · `Record Types` · `Reports & Dashboards` · `Sharing Settings (OWD)` · `Profiles`
 
---
 
<p align="center">Feito com 💙 e muito café ☕ durante o Programa de Formação de Consultores
 
