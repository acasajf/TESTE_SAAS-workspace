# Tela: Kanban Comercial - Proposta

## Identificacao

- **Modulo:** Comercial / CRM.
- **Tela:** Modal de Proposta aberto a partir do Kanban Comercial.
- **Estado analisado:** proposta em rascunho, com dados comerciais preenchidos.
- **Card de origem:** "construtora teste".

## Objetivo da etapa

A etapa "Proposta" serve para transformar o interesse do lead em uma proposta comercial concreta. Nesta etapa o usuario deve definir o escopo, valor, prazo, validade, condicao de pagamento, forma de envio e acompanhar se a proposta ja foi enviada ou se ainda aguarda retorno.

## Campos e controles visiveis

| Elemento | Tipo | Valor observado | Observacao |
|---|---|---|---|
| Titulo | texto | construtora teste | Nome do cliente/lead. |
| Status da etapa | texto | Proposta | Indica a coluna atual do funil. |
| Servicos | checkboxes | LTCAT, Treinamento, Plano de acao, Visita tecnica, Relatorio tecnico, Outro | Nenhum checkbox aparece marcado no screenshot. |
| Escopo / descricao dos servicos | textarea | FAFAFAFA | Descricao da entrega proposta. |
| Valor da proposta | moeda/texto | 1500,00 | Valor comercial da proposta. |
| Prazo de entrega | numero/texto | 10 | Provavel prazo em dias. |
| Validade | data | 28/06/2026 | Data limite da proposta. |
| Condicao de pagamento | radio | 50% entrada + 50% entrega | Opcao selecionada. |
| Forma de envio | chips | E-mail | Opcao selecionada. |
| Status da proposta | chips/status | Rascunho | Status interno selecionado. |
| Agenda | lista | LIGACAO - 18/06/2026 - ligar para cliente | Follow-up comercial registrado. |

## Acoes disponiveis

| Botao | Resultado esperado |
|---|---|
| Salvar Proposta | Persiste os dados e mantem a oportunidade em "Proposta". |
| Gerar PDF | Gera documento formal da proposta. |
| Enviar ao Cliente | Envia ou prepara o envio da proposta formal ao cliente ainda na etapa "Proposta". |
| Enviar para Negociacao | Move a oportunidade para a etapa seguinte do funil. |
| Marcar como Perdido | Move a oportunidade para "Perdido". |
| Fechar | Fecha o modal sem acao de avancar. |

## Status da proposta

| Status | Significado recomendado | Pode enviar para negociacao? |
|---|---|---|
| Rascunho | Proposta ainda em montagem, nao formalizada ao cliente. | Nao. Deve salvar ou gerar PDF antes. |
| Enviada | Proposta ja foi enviada ao cliente por WhatsApp, e-mail ou PDF. | Talvez. Avancar se houver conversa comercial ativa. |
| Aguardando retorno | Proposta enviada e em follow-up, esperando resposta do cliente. | Talvez. Avancar quando o cliente responder com interesse, ajustes ou contraproposta. |

## Tratamento do retorno do cliente

Depois que a proposta for enviada ao cliente, a oportunidade nao deve avancar automaticamente. O retorno precisa ser classificado pelo usuario ou pelo sistema:

| Retorno do cliente | Acao recomendada | Etapa/status resultante |
|---|---|---|
| Cliente ainda nao respondeu | Registrar follow-up e manter acompanhamento. | Status "Aguardando retorno" na etapa "Proposta". |
| Cliente pediu desconto, ajuste de escopo, alteracao de prazo ou forma de pagamento | Abrir tratativa comercial. | Mover para etapa "Negociacao". |
| Cliente aceitou a proposta sem ajustes | Avancar para formalizacao do contrato/servico. | Mover para etapa "Formalizacao". |
| Cliente recusou ou informou que nao tem interesse | Registrar motivo da perda. | Mover para etapa "Perdido". |
| Cliente pediu nova versao da proposta | Duplicar/revisar proposta ou voltar status para edicao controlada. | Manter em "Proposta" com nova versao em "Rascunho" ou "Enviada". |

## Regra recomendada apos envio

Ao clicar em "Enviar ao Cliente" com sucesso:

1. Marcar a forma de envio utilizada.
2. Registrar data/hora do envio.
3. Atualizar o status da proposta para "Enviada".
4. Criar ou sugerir um follow-up na agenda.
5. Manter o card na etapa "Proposta" ate haver retorno.

Se o prazo de follow-up chegar sem resposta, o status deve passar para "Aguardando retorno" ou permanecer nele, destacando a necessidade de nova acao comercial.

## Regra recomendada antes de enviar para Negociacao

Antes de clicar em "Enviar para Negociacao", o usuario deveria confirmar:

1. Escopo/descricao dos servicos preenchido de forma compreensivel.
2. Valor da proposta definido.
3. Prazo de entrega definido.
4. Validade definida e ainda vigente.
5. Condicao de pagamento selecionada.
6. Forma de envio selecionada.
7. Proposta salva.
8. PDF gerado quando o processo comercial exigir documento formal.
9. Status da proposta atualizado para "Enviada" ou "Aguardando retorno".
10. Existencia de uma interacao do cliente que caracterize negociacao: pedido de desconto, ajuste de escopo, duvida sobre prazo, contraproposta, aprovacao condicionada ou discussao de forma de pagamento.

## Interpretacao do estado observado

No screenshot, o status interno ainda esta como "Rascunho". Apesar de o botao "Enviar para Negociacao" estar disponivel, a leitura de negocio mais segura e que a proposta ainda nao deveria avancar. O fluxo ideal seria:

1. Revisar e completar servicos marcados.
2. Salvar proposta.
3. Gerar PDF, se aplicavel.
4. Enviar ao cliente por E-mail, WhatsApp ou outro canal.
5. Alterar status para "Enviada".
6. Registrar follow-up na agenda.
7. Ao receber retorno ou iniciar tratativa, mudar para "Aguardando retorno" ou enviar para "Negociacao", conforme a regra do time comercial.

## Risco de UX identificado

O botao "Enviar para Negociacao" aparece mesmo com status "Rascunho". Isso pode permitir avancar uma oportunidade antes de a proposta ter sido efetivamente enviada. Uma validacao recomendada seria bloquear ou confirmar a acao quando:

- Status da proposta = "Rascunho".
- PDF ainda nao foi gerado, se obrigatorio.
- Forma de envio nao foi selecionada.
- Nao existe agenda/follow-up ou registro de envio.

## Perguntas para validacao humana

1. O sistema deve permitir "Enviar para Negociacao" com status "Rascunho"?
2. "Gerar PDF" e obrigatorio antes de enviar a proposta ao cliente?
3. O status "Enviada" e atualizado automaticamente apos envio por e-mail/WhatsApp ou manualmente pelo usuario?
4. "Aguardando retorno" significa proposta enviada sem resposta ou resposta inicial do cliente?
5. A etapa "Negociacao" deve iniciar apenas quando o cliente pede ajuste/condicao, ou ja quando a proposta e enviada?
