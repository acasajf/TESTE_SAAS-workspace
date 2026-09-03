# Tela: Kanban Comercial - Novo Lead

## Identificacao

- **Modulo:** Comercial / CRM.
- **Tela:** Modal de Novo Lead aberto a partir do Kanban Comercial.
- **Estado analisado:** lead existente ou recem-criado com dados preenchidos parcialmente.
- **Card de origem:** "construtora teste" na coluna "Novo Lead".

## Contexto no Kanban

O Kanban Comercial apresenta um funil com sete etapas:

1. Novo Lead
2. Atendimento
3. Proposta
4. Negociacao
5. Formalizacao
6. Ganho
7. Perdido

No estado observado, apenas "Novo Lead" possui 1 card. As demais etapas exibem estado vazio com a mensagem "Nenhuma oportunidade nesta etapa".

## Card na coluna Novo Lead

### Informacoes visiveis

| Elemento | Valor observado | Observacao |
|---|---|---|
| Nome do lead | construtora teste | Nome em destaque no card. |
| Servico | TREINAMENTOS | Exibido logo abaixo do nome. |
| Telefone | 32988422474 | Exibido com icone de telefone. |
| Follow-up | Sem follow-up | Estado de acompanhamento pendente/ausente. |
| Acao WhatsApp | WhatsApp | Atalho de contato. |
| Acao excluir | icone de lixeira | Acao destrutiva disponivel no card. |
| Acao avancar | "Avan..." | Provavel botao para avancar etapa, texto truncado no screenshot. |

### Inferencia funcional

O card representa uma oportunidade ainda nao qualificada. Ele permite contato rapido por WhatsApp, remocao e possivel avancar de etapa. O status "Sem follow-up" sugere que o sistema rastreia proxima acao ou lembrete comercial.

## Modal Novo Lead

### Cabecalho

| Elemento | Valor observado | Regra aparente |
|---|---|---|
| Titulo | construtora teste | Reflete o valor do campo "Nome / empresa". |
| Subtitulo/status | Novo Lead | Indica a etapa atual do funil. |
| Fechar | X | Fecha o modal sem acao primaria visivel. |

### Campos

| Campo | Tipo | Obrigatorio | Valor/placeholder observado | Observacoes |
|---|---|---|---|---|
| Nome / empresa | texto | Sim | construtora teste | Marcado com asterisco vermelho. |
| Telefone / WhatsApp | texto/telefone | Nao visivel | 32988422474 | Campo usado para contato comercial. |
| E-mail | email | Nao visivel | contato@empresa.com | Placeholder permanece quando vazio. |
| Servico de interesse | chips + texto | Sim | TREINAMENTOS | Chips visiveis: PGR, PCMSO, LTCAT, Laudo tecnico, Treinamento, Licenca ambiental, Outro. |
| Origem do lead | chips | Nao visivel | nao selecionado | Chips visiveis: Indicacao, Site / SEO, Redes sociais, WhatsApp, LinkedIn, Ligacao ativa, Evento, Parceiro, Outro. |
| Observacao inicial | textarea | Nao visivel | Ex: Cliente entrou em contato pelo WhatsApp pedindo orcamento de PGR urgente... | Campo para registrar contexto inicial. |

### Acoes

| Botao | Tipo funcional | Resultado esperado |
|---|---|---|
| Iniciar Atendimento -> | Primario | Salva/qualifica o lead e move para "Atendimento". |
| Salvar Lead | Secundario | Persiste dados mantendo o lead em "Novo Lead". |
| Marcar como Perdido | Alternativo/descarte | Move o lead para a etapa "Perdido". |

## Validacoes visiveis

- "Nome / empresa" aparenta ser obrigatorio.
- "Servico de interesse" aparenta ser obrigatorio.
- Nao ha mensagens de erro ou sucesso no estado capturado.
- Nao ha indicacao visual de campos invalidos no screenshot.

## Estados documentados

### Estado preenchido parcial

- Nome preenchido.
- Telefone preenchido.
- E-mail vazio com placeholder.
- Servico selecionado/preenchido como "TREINAMENTOS".
- Origem nao selecionada.
- Observacao vazia com placeholder.

### Estado de funil vazio nas demais etapas

- Atendimento, Proposta, Negociacao, Formalizacao, Ganho e Perdido exibem estado vazio.
- Cada coluna mantem contador "0".

## Regras de negocio inferidas

- Todo lead nasce ou permanece inicialmente em "Novo Lead".
- O lead pode ser salvo sem iniciar atendimento imediatamente.
- A transicao para "Atendimento" parece ser uma acao explicita, nao automatica.
- Um lead pode ser marcado como perdido diretamente a partir de "Novo Lead".
- O pipeline financeiro no topo permanece "R$ 0,00" enquanto nao ha proposta/valor associado ou oportunidade ganha.

## Perguntas para validacao humana

1. "Iniciar Atendimento" tambem salva alteracoes pendentes antes de mover o card?
2. O botao "Salvar Lead" fecha o modal ou apenas salva mantendo-o aberto?
3. "Marcar como Perdido" exige confirmacao ou motivo de perda em outro estado/modal?
4. O campo textual de servico permite valor livre quando "Outro" e selecionado?
5. O status "Sem follow-up" e calculado por ausencia de data de proximo contato?

