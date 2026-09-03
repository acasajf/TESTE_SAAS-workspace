# Inventario de UI - Kanban Comercial

> Este inventario agrega telas documentadas em momentos diferentes. A secao "Finder publico - mapa de busca" foi adicionada em 2026-07-16 a partir de um novo screenshot.

## Finder publico - mapa de busca

- **Proposito:** permitir que visitantes encontrem profissionais e empresas de SST e Meio Ambiente por localizacao e termo de busca.
- **Estado observado:** mapa carregado, busca ainda nao executada, campos vazios e marcadores distribuidos pelo Brasil.
- **Contexto de uso:** visitante acessa a pagina publica do SST Finder em desktop, antes de autenticar.
- **Elementos principais:**
  - Cabecalho flutuante preto com logo SSTFinder.
  - Links "App SSTFinder", "Como Funciona", "Planos" e "Entrar".
  - CTA primario "Comecar Agora".
  - Card de busca sobreposto ao mapa.
  - Campo de localizacao com placeholder "Localizacao, bairro ou cidade...".
  - Campo de termo/categoria com placeholder "O que voce procura?".
  - Acoes "Buscar", "Filtros" e "Limpar busca".
  - Botao circular de localizacao/geolocalizacao.
  - Mapa da America do Sul centralizado no Brasil.
  - Marcadores de duas categorias visuais: SST/capacete e Meio Ambiente/folha.

Detalhamento completo: [screens/finder-mapa-busca.md](screens/finder-mapa-busca.md).

## Fonte visual analisada

- Screenshot 1: Kanban Comercial com colunas de funil e um card em "Novo Lead".
- Screenshot 2: Modal/formulario do card "construtora teste", estado "Novo Lead".
- Screenshot 3: Rodape de acoes da etapa "Proposta".
- Screenshot 4: Modal/formulario do card "construtora teste", estado "Proposta".

## Telas identificadas

### Kanban Comercial

- **Proposito:** acompanhar oportunidades comerciais por etapa do funil.
- **Estado observado:** preenchido parcialmente, com 1 oportunidade em "Novo Lead" e demais colunas vazias.
- **Contexto de uso:** usuario acessa a area comercial para visualizar pipeline, abrir cards, atualizar dados e avancar oportunidades.
- **Elementos principais:**
  - Titulo "KANBAN COMERCIAL".
  - Indicador de pipeline no canto superior direito com valor "R$ 0,00".
  - Botao "Atualizar".
  - Colunas do funil: "Novo Lead", "Atendimento", "Proposta", "Negociacao", "Formalizacao", "Ganho", "Perdido".
  - Contadores por coluna.
  - Estado vazio por coluna: "Nenhuma oportunidade nesta etapa".
  - Card de lead com empresa, servico, telefone, status de follow-up e acoes rapidas.

### Modal Novo Lead

- **Proposito:** cadastrar, revisar ou qualificar um lead antes de iniciar atendimento.
- **Estado observado:** preenchido parcialmente, com nome, telefone e servico selecionado/preenchido.
- **Contexto de uso:** usuario abre/cria um card na coluna "Novo Lead" do Kanban Comercial.
- **Elementos principais:**
  - Cabecalho com nome do lead "construtora teste" e status "Novo Lead".
  - Acao de fechar no canto superior direito.
  - Campo obrigatorio "Nome / empresa".
  - Campos "Telefone / WhatsApp" e "E-mail".
  - Selecao de "Servico de interesse" por chips e campo textual do servico escolhido.
  - Selecao de "Origem do lead" por chips.
  - Campo "Observacao inicial".
  - Acoes finais: "Iniciar Atendimento", "Salvar Lead", "Marcar como Perdido".

### Modal Proposta

- **Proposito:** montar, registrar e controlar o envio de uma proposta comercial antes da negociacao.
- **Estado observado:** proposta em preenchimento com status interno "Rascunho" selecionado.
- **Contexto de uso:** usuario abre um card que ja avancou ate a coluna/etapa "Proposta".
- **Elementos principais:**
  - Cabecalho com nome do lead "construtora teste" e status "Proposta".
  - Lista de servicos por checkbox.
  - Campo de escopo/descricao dos servicos.
  - Campos de valor, prazo de entrega e validade.
  - Condicao de pagamento por radio buttons.
  - Forma de envio por chips: WhatsApp, E-mail, PDF gerado.
  - Status interno da proposta: Rascunho, Enviada, Aguardando retorno.
  - Agenda com atividade de ligacao.
  - Acoes finais: "Salvar Proposta", "Gerar PDF", "Enviar para Negociacao", "Marcar como Perdido", "Fechar".
