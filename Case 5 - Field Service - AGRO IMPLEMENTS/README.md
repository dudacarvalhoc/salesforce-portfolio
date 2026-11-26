# 🚜 AgroField: Field Service para a AGRO IMPLEMENTS
 
> Implementação do **Salesforce Field Service** para uma indústria de implementos agrícolas: do chamado do cliente até o relatório de serviço assinado no celular do técnico. 📱✍️
 
![Salesforce](https://img.shields.io/badge/Salesforce-Field%20Service-00A1E0?logo=salesforce&logoColor=white)
![Flows](https://img.shields.io/badge/Automação-Flow%20Builder-032D60)
![Mobile](https://img.shields.io/badge/App-Field%20Service%20Mobile-7F3FBF)
![Status](https://img.shields.io/badge/status-MVP%20entregue-2E7D32)
 
---
 
## 📌 Sobre o projeto
 
Este repositório documenta o **Case 5** do programa de formação de consultores Salesforce do **Grupo Taak**.
 
A **AGRO IMPLEMENTS** (empresa fictícia do case) é uma grande fabricante de implementos agrícolas. Ela precisava de um **atendimento em campo robusto, rápido e simples**, pensado para técnicos que usam o celular em condições nada favoráveis: no meio da fazenda, com pouco sinal e com pressa. 🌾
 
O objetivo foi entregar um **MVP** com dois fluxos de ponta a ponta:
 
| Fluxo | Descrição |
|---|---|
| 🔧 **Instalação de Máquinas** | Venda → Caso → OT → Compromisso → Técnico instala, aponta tarefas, horas e fotos → Relatório enviado ao cliente |
| 🛠️ **Manutenção Corretiva** | Chamado → SAC não resolve por telefone → OT → Técnico aponta peças, tarefas e fotos → Cliente assina → OT finalizada |
 
> 📝 **Regra do case:** Caso, Ordem de Trabalho e alocação do técnico são feitos no **Desktop**. Todo o resto acontece no **app Field Service Mobile**.
 
---
 
## 🗺️ Visão geral do fluxo
 
```
🏢 Conta  ──►  📞 Caso  ──►  📋 Ordem de Trabalho  ──►  📅 Compromisso  ──►  👷 Técnico (app)
 (território     (herda          (criada pelo             (criado              (aponta, fotografa,
  e catálogo)    dados da         botão no Caso)           automaticamente)      assina e conclui)
                 conta)
```
 
Cada etapa **preenche a próxima sozinha**: o SAC digita pouco e o técnico só executa. ⚡
 
---
 
## 🧱 O que foi construído
 
### 1. ⚙️ Setup inicial
- Instalação do **pacote gerenciado do Field Service**
- Permission sets do FSL (Admin, Dispatcher e Resource)
- **Field Service Mobile Settings** com a identidade visual da AGRO (cores da marca, geolocalização, cache e notificações)
- Regras de geocodificação, Deliverability e endereço de e-mail da organização
### 2. 📦 Carga de dados
- **8 produtos** com código, como Kit Reparo, Braço Hidráulico, Pneu Dianteiro e Fusíveis Elétricos
- **2 catálogos de preço** (São Paulo e Rio de Janeiro), com preços diferentes por região 💰
- **Contas e contatos** com endereço de entrega, território e catálogo
- Tratamento dos dados da planilha: CEPs sem o zero inicial e país faltando
### 3. 🌎 Territórios e técnicos
 
| Território | Horário de operação | Técnico |
|---|---|---|
| 🏙️ São Paulo | Seg a sex, 08h às 17h | Técnico São Paulo |
| 🏖️ Rio de Janeiro | Seg a sex, 09h às 18h | Técnico Rio de Janeiro |
 
- Usuários técnicos com licenças de Scheduling e Mobile
- Recursos de serviço vinculados como **membros primários** de cada território
- **Tipos de Trabalho** (as "tarefas"): Inspeção inicial, Montagem da máquina, Teste de funcionamento, Treinamento do operador, Diagnóstico de falha e Troca de peça
### 4. 🏢 Conta
- Lookups obrigatórios de **Território de Serviço** e **Catálogo de Preço**
- Layout enxuto, com a seção **Field Service**
### 5. 📞 Caso
- Picklists personalizadas: **Status** (Em triagem, Serviço agendado, Fechado), **Tipo** (Instalação, Manutenção) e **Origem** (Telefone, Email, Web)
- Campo de **Endereço de atendimento** e checkbox **"Usar endereço da conta?"** 🏠
- Obrigatoriedade definida no **layout**
- Flow que puxa **território, catálogo e endereço** da conta
### 6. 📋 Ordem de Trabalho
- Botão **"Criar Ordem de Trabalho"** no Caso, com valores pré-carregados
- **Record Types** de Instalação e Manutenção, cada um com seu layout:
  - Instalação → **Equipamento Instalado?**
  - Manutenção → **Equipamento liberado para Uso**
- Campos: Produto, Tipo, Parecer da Visita, Realizar atendimento em campo?
- Status: Novo, Agendado, Atribuído, Em Andamento e Completo
### 7. 🔩 Itens da OT (Peças e Tarefas)
- Record Types **Peças** e **Tarefas**
- Apontamento de **horas trabalhadas** por tarefa ⏱️
- Botões **Apontar Peças** e **Apontar Tarefas**, disponíveis no app
### 8. 📅 Compromisso de Serviço automático
- Criado por Flow quando a OT é marcada para **atendimento em campo**
- Janela de horário: início permitido em **D+1, +1h**, e vencimento em **D+1, +4h**
### 9. 🖥️ Console do Dispatcher
- **Scheduling Console** com os dois territórios, o Gantt e o mapa
- Alocação manual do técnico arrastando o compromisso para a agenda 🖱️
- Política de agendamento **Customer First**
### 10. 📄 Relatório de Serviço
- Modelo personalizado com dados do cliente, atendimento, parecer, itens e totais
- **Duas assinaturas**: técnico e cliente ✍️✍️
- Gerado no app, a partir do compromisso
### 11. 📧 E-mails ao cliente
 
| E-mail | Quando dispara |
|---|---|
| ✅ Atendimento agendado | Compromisso → **Scheduled** |
| 📎 Relatório de Serviço (PDF anexado) | Compromisso → **Completed** |
| 🎉 Serviço concluído | OT → **Completo** |
 
### 12. 🔐 Segurança e validações
- Perfil **Técnico de Campo**: **vê todas** as OTs e compromissos, mas **só edita os seus** 👀✋
- Flow que **compartilha a OT e o compromisso** com o técnico no momento do agendamento
- **Validação 1:** o status do compromisso precisa seguir a ordem *Nenhum → Agendado → Despachado → Em andamento → Concluído*
- **Validação 2:** a OT não pode ser concluída sem parecer da visita e sem a situação do equipamento
- Transições de status configuradas no **Service Appointment Life Cycle**
### 13. 📸 Desafio: fotos pelo app
- **Field Service Mobile Flow** com upload de arquivos (câmera)
- Publicado como **App Extension** no Field Service Mobile
### 14. 📱 Experiência no app
- List views para o técnico: **My Jobs** (disponível off-line), por território, agendados e pendentes
- Ações rápidas pelo botão **⚡ Ações**
### 15. 📊 Relatórios e painéis
- Relatório **Plano de Tarefas – Ordens de Trabalho** (OTs com seus itens)
- Painel **Field Service – AGRO IMPLEMENTS** (OTs por status)
---
 
## 🤖 Automações (Flows)
 
| Flow | Objeto | O que faz |
|---|---|---|
| Caso - Preencher dados da Conta | Case | Copia território, catálogo e endereço da conta |
| OT - Preencher a partir do Caso | Work Order | Define tipo, record type, endereço e modelo de relatório |
| OT - Criar Compromisso de Serviço | Work Order | Cria o compromisso com a janela de horário |
| Compartilhar OT com Técnico | Assigned Resource | Dá edição da OT e do compromisso ao técnico alocado |
| Compromisso - E-mail Agendado | Service Appointment | E-mail de confirmação do agendamento |
| Compromisso - Enviar Relatório | Service Appointment | E-mail com o relatório assinado em PDF |
| OT - E-mail Serviço Executado | Work Order | E-mail de serviço concluído |
| Anexar Fotos Mobile | Field Service Mobile Flow | Upload de fotos pelo app |
 
---
 
## 🧠 Decisões e desafios técnicos
 
Nem tudo saiu como no manual, e esses contornos fizeram parte do aprendizado 💪
 
| Desafio | Solução |
|---|---|
| Campo padrão **Product** do Caso travado na org | Lookup customizado **Produto** |
| **Price Book** não aceita lookup obrigatório | Obrigatoriedade por **validation rule** + layout |
| **Classic Dispatch Console** descontinuado | Uso do **Scheduling Console** |
| Técnico sem permissão para editar a OT no app | Flow de **compartilhamento** no momento do agendamento |
| Botões de apontamento sem aparecer no app | Ajuste de **record types e permissões** no perfil do técnico |
| Campos de estado e país recebendo texto | Mapeamento para os campos de **código** (State/Country Code) |
| Limite de e-mails e de armazenamento da org Developer | Monitoramento pela API **/limits** e limpeza de arquivos |
| "Manutenção Preventiva" × "Corretiva" no escopo | Tudo tratado como **Manutenção Corretiva**, por orientação da liderança |
 
---
 
## 🧪 Como testar o fluxo
 
1. 🏢 Crie uma **conta** com território e catálogo (tente salvar sem eles para ver a validação)
2. 📞 Abra um **caso** de Instalação e marque *"Usar endereço da conta?"*
3. 📋 Clique em **Criar Ordem de Trabalho** e marque *"Realizar atendimento em campo?"*
4. 🖥️ No **Scheduling Console**, arraste o compromisso para o técnico e faça o **Dispatch** → 📧 e-mail 1
5. 📱 No **app**, logado como técnico:
   - Status **In Progress**
   - **Apontar Tarefas** (com horas) e **Apontar Peças**
   - **Anexar Fotos**
   - Preencher o **Parecer da Visita**
   - **Criar relatório de serviço** com as 2 assinaturas
   - Compromisso **Completed** → 📧 e-mail 2 · OT **Completo** → 📧 e-mail 3
6. 🔐 Logado como Técnico SP, tente editar um compromisso do **RJ** → acesso negado ✋
---
 
## 🛠️ Ferramentas utilizadas
 
- ☁️ Salesforce **Field Service** (pacote gerenciado FSL)
- 📱 **Field Service Mobile** (iOS)
- 🔀 **Flow Builder** (record-triggered, screen e field service mobile flows)
- 🔎 **Salesforce Inspector Reloaded**
- 🎓 **Trailhead**: Install the Field Service Managed Package, Dispatcher Console for Dispatchers, Dispatcher Console for Admins e Field Service Mobile
---
 
## 👩‍💻 Autora
 
**Maria Eduarda Cortellini (Duda)**
Estagiária de consultoria Salesforce · Estudante de Ciência da Computação
 
> 💜 *"Do zero ao app do técnico: aprendendo Field Service na prática, um erro de cada vez."*
 
