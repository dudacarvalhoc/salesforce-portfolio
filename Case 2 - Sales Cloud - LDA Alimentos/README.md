# Case LDA Alimentos: Implementação Salesforce Sales Cloud

Projeto de treinamento do programa de Formação de Consultores Funcionais do Grupo Taak. O objetivo foi implementar, em uma org Salesforce, o processo comercial de uma distribuidora de alimentos fictícia, a LDA Alimentos, do cadastro do cliente até o pedido encerrado.

## O desafio

A LDA Alimentos não tinha visão única de contas, pedidos e descontos. A solução precisava:

- Centralizar clientes, contatos, produtos e pedidos no Salesforce
- Calcular automaticamente preço, frete, imposto e margem de cada item, de acordo com a rota (estado de origem e destino) e o tipo de cliente
- Controlar descontos por meio de um processo de aprovação
- Automatizar o ciclo de vida do pedido (status e datas)
- Entregar relatórios e painéis prontos para a gestão

## Visão geral da solução

| Área | O que foi construído |
|---|---|
| Dados | Objetos padrão customizados (Conta, Contato, Pedido, Produto do Pedido, Produto, Catálogo) e 3 objetos customizados de consulta: Tabela de Impostos, Tabela de Frete e Tabela de Margem |
| Automação | 8 Flows ativos, Validation Rules e 1 Processo de Aprovação |
| Dados de carga | Carga em massa via Salesforce Inspector Reloaded (contas, contatos, produtos, catálogos, impostos, fretes e margens) |
| Relatórios | 15 relatórios na pasta "Relatório de Vendas LDA" |
| Painéis | 2 painéis na pasta "Painéis de venda LDA" |
| Interface | Tema "LDA Alimentos", aplicativo Lightning com 10 itens de navegação e Página Inicial |

## Lógica de cálculo

Imposto, frete e margem variam por rota e por tipo de cliente. Em vez de fixar esses valores na automação, eles ficam em três tabelas de consulta que o Flow acessa a cada item de pedido criado ou atualizado:

| Tabela | Objeto | Chave de consulta |
|---|---|---|
| Impostos | `TaxTable__c` | Estado de origem + Estado de destino + Tipo de cliente |
| Frete | `FreightTable__c` | Estado de origem + Estado de destino |
| Margem | `MarginTable__c` | Estado de origem + Estado de destino + Tipo de cliente |

```
Preço por Unidade = (Preço de tabela + Frete) × (1 + Imposto%) × (1 + Margem%)
Preço de Venda    = Preço por Unidade × ((100 − Desconto) / 100)
```

Se uma regra de negócio mudar, basta editar a linha da tabela correspondente, sem alterar o Flow.

## Automações

### Flows ativos (8)

| Flow | Objeto | Função |
|---|---|---|
| Pedido - Criar - Atualizar - Endereço e Datas | Pedido | Copia o endereço da Conta ou do Contato conforme o checkbox, preenche as datas iniciais só na criação e atribui o catálogo de preços pelo tipo de cliente |
| Contato - Criar - Atualizar - Endereço de Contato | Contato | Copia o endereço da Conta quando o checkbox está marcado |
| Produto do Pedido - Criar e Atualizar - Soma | Produto do Pedido | Consulta as tabelas de imposto, frete e margem e calcula preço por unidade e preço de venda |
| Pedido Recalcula Itens ao Mudar Estado | Pedido | Quando o estado de origem ou destino muda, percorre os itens do pedido em um Loop e força o recálculo |
| PedidoAtualizaStatus | Pedido | Muda o status de Rascunho para Em progresso quando o pedido passa a ter valor |
| Pedido - Atualiza Data Termino | Pedido | Preenche a data de término quando o status vira Encerrado |
| Conta - Atualiza Data Primeiro Pedido | Conta | Grava a data da primeira compra quando o primeiro pedido da conta é encerrado |
| Preencher Data Primeira Compra Conta | Conta | Preenche a data da primeira compra na criação da conta |

### Flows desativados (3)

Versões anteriores, substituídas por versões finais e mantidas apenas como histórico:

- Calcular Valores do Produto do Pedido
- Copiar Endereço da Conta para Contato
- ProdutoPedido - AtualizaStatusPedido (substituído pelo PedidoAtualizaStatus, porque a tela "Add Products" não dispara Flow de criação no Produto do Pedido)

### Validation Rules

| Regra | Objeto | Função |
|---|---|---|
| `Validador_CNPJ` | Conta | Valida o formato do CNPJ |
| `Validador_CaixaSelecao` | Pedido | Impede marcar os dois checkboxes de endereço ao mesmo tempo |
| `BloqueioEdicaoDatasIniciais` | Pedido | Bloqueia a edição de Order Start Date e PO Date depois da criação |
| `EnderecoDestinoObrigatorio` | Pedido | Exige o endereço de destino completo, a menos que um dos checkboxes de endereço esteja marcado |

### Processo de Aprovação

`AprovacaoDesconto` (Pedido): entra quando o maior desconto do pedido é ≥ 5%.

- Envio: Status → Aguardando aprovação
- Aprovação final: Status → Aprovado
- Rejeição final: Status → Em progresso
- Aprovador escolhido manualmente por quem envia

## Relatórios e painéis

- **15 relatórios:** quantidade total de pedidos, pedidos por status (Rascunho, Em progresso, Em aprovação, Aprovados, Encerrados), produtos vendidos (total e por tipo de cliente), pedidos em aberto e pedidos finalizados
- **Painel "Visualização Geral dos Pedidos":** 12 métricas (quantidade e valor, total e por status)
- **Painel "Produtos Vendidos":** 6 gráficos de funil (total + 5 tipos de cliente)
- **Página Inicial:** 3 gráficos de relatório

## Principais aprendizados

- Em Flows, o seletor visual de campos pode resolver `Order ID` para `OriginalOrderId` (sempre nulo). Solução: criar um recurso de Fórmula com `{!$Record.Id}` ou `{!$Record__Prior.Id}`
- A tela "Add Products" não dispara Flows de criação no Produto do Pedido de forma confiável. O gatilho de status foi movido para o Pedido
- Um Update que reenvia valores idênticos não dispara outro Flow. No Loop de recálculo foi preciso um Assignment que altera um campo para forçar o disparo
- `Order.Status` está ligado ao `StatusCode` nativo: na criação só aceita valores mapeados como Draft, e o pedido precisa de pelo menos 1 produto para ser ativado
- Produtos só entram em catálogo customizado se antes tiverem entrada no Standard Price Book
- Em caso de comportamento estranho em campo obrigatório, testar em janela anônima pode revelar que o problema é cache do navegador

## Pendências conhecidas

- Testes visuais dos objetos de tabela (impostos, frete e margem)
- Confirmar com o mentor a interpretação da caixa "tema da organização" no aplicativo
- Limpar registros de teste e linhas duplicadas na Tabela de Frete
- Excluir o Flow antigo `ProdutoPedido - AtualizaStatusPedido`

## Tecnologias

Salesforce Sales Cloud · Flow Builder · Validation Rules · Approval Processes · Reports & Dashboards · Lightning App Builder · Salesforce Inspector Reloaded
