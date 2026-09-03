# Spec visual - Finder publico no mapa

Data da analise: 2026-07-16

## 1. Identificacao

| Item | Descricao | Confianca |
|---|---|---|
| Tela | Pagina publica do SST Finder com busca geografica | Confirmado visualmente |
| Ambiente observado | Desktop, navegador em `localhost:3000` | Confirmado visualmente |
| Estado | Mapa carregado, formulario vazio, marcadores visiveis | Confirmado visualmente |
| Usuario provavel | Visitante nao autenticado procurando prestadores | Inferido |
| Area geografica | America do Sul, com foco no Brasil | Confirmado visualmente |

## 2. Proposito da tela

A tela funciona como porta de entrada publica para descobrir profissionais e empresas de Saude e Seguranca do Trabalho (SST) e Meio Ambiente. A proposta de valor combina busca textual, localizacao e exploracao direta no mapa.

## 3. Estrutura visual

### Cabecalho

- Barra preta flutuante, com cantos bem arredondados e margem lateral.
- Logo circular seguido do nome `SSTFinder`, com `SST` em verde e `Finder` em branco.
- Navegacao central: `App SSTFinder`, `Como Funciona` e `Planos`.
- Acoes a direita: icone de globo, `Entrar` e CTA branco `Comecar Agora`.

### Card de busca

- Card claro sobreposto ao mapa, centralizado na regiao superior.
- Titulo em duas linhas: `Encontre profissionais e empresas de SST e Meio Ambiente perto de voce`.
- Dois campos horizontais empilhados:
  - localizacao, bairro ou cidade;
  - termo, especialidade ou servico procurado.
- Linha de acoes com:
  - `Buscar`, em verde;
  - `Filtros`, secundario;
  - `Limpar busca`, terciario.
- Botao circular separado, a direita do card, com icone semelhante a navegacao/localizacao.

### Mapa e marcadores

- Mapa ocupa todo o restante da viewport e permanece atras do cabecalho e da busca.
- O Brasil aparece como centro visual.
- Ha marcadores principalmente nas regioes Norte, Nordeste, Centro-Oeste, Sudeste e Sul.
- Sao observadas duas familias de marcador:
  - contorno azul-petroleo com icone de capacete, associado visualmente a SST;
  - contorno verde com icone de folha, associado visualmente a Meio Ambiente.
- Nao ha legenda visivel explicando formalmente essa codificacao.

## 4. Controles observados

| Elemento | Tipo aparente | Estado | Acao esperada |
|---|---|---|---|
| App SSTFinder | Link de navegacao | Ativo/normal | Abrir area ou explicacao do app |
| Como Funciona | Link de navegacao | Normal | Abrir explicacao do produto |
| Planos | Link de navegacao | Normal | Abrir planos comerciais |
| Entrar | Link/botao | Normal | Abrir autenticacao |
| Comecar Agora | CTA primario | Habilitado | Iniciar cadastro ou adesao |
| Localizacao | Campo de texto/autocomplete | Vazio | Informar cidade, bairro ou local |
| O que voce procura? | Campo de texto/autocomplete | Vazio | Informar servico, categoria ou termo |
| Buscar | Botao primario | Habilitado | Aplicar criterios e atualizar resultados |
| Filtros | Botao secundario | Habilitado | Exibir filtros adicionais |
| Limpar busca | Botao terciario | Habilitado visualmente | Restaurar estado inicial |
| Geolocalizacao | Botao de icone | Habilitado | Centralizar pelo local atual |
| Marcador | Ponto interativo | Normal | Abrir resumo de resultado |

Obrigatoriedade, validacoes, autocomplete e destinos nao podem ser confirmados pelo screenshot.

## 5. Hierarquia e linguagem visual

- A combinacao preto, branco, verde e azul-petroleo cria identidade coerente com os temas de SST e Meio Ambiente.
- O CTA `Comecar Agora` e o botao `Buscar` possuem boa diferenciacao.
- O titulo explica rapidamente o beneficio principal.
- O mapa tem baixo contraste, o que reduz ruido e favorece os marcadores.
- O card de busca concentra corretamente a tarefa principal, mas compete verticalmente com o cabecalho em telas de menor altura.

## 6. Pontos fortes

1. Proposta de valor aparece antes de qualquer interacao.
2. Busca por localizacao e necessidade cobre os dois criterios mais importantes.
3. Marcadores diferenciados sugerem categorias sem sobrecarregar o mapa.
4. O mapa comunica cobertura nacional e disponibilidade de prestadores.
5. As acoes de conversao, autenticacao e exploracao estao presentes na primeira dobra.

## 7. Riscos e oportunidades de melhoria

| Prioridade | Achado | Impacto | Recomendacao |
|---|---|---|---|
| Alta | Nao existe legenda visivel para capacete e folha | Usuario pode nao entender as categorias | Adicionar legenda compacta e acessivel |
| Alta | Nao ha contagem ou lista de resultados visivel | O mapa sozinho dificulta comparar prestadores e usar teclado | Exibir quantidade e painel/lista sincronizada |
| Alta | `Limpar busca` parece habilitado com campos vazios | Acao sem efeito pode gerar ruido | Desabilitar no estado inicial |
| Media | Placeholder e icones dos campos apresentam contraste baixo | Leitura e acessibilidade podem ser prejudicadas | Aumentar contraste conforme WCAG |
| Media | Botao de geolocalizacao usa apenas icone | Funcao pode ser ambigua | Adicionar tooltip e nome acessivel |
| Media | Marcadores proximos no Sudeste se sobrepoem | Se houver mais resultados, a selecao fica dificil | Implementar clustering e expansao por zoom |
| Media | Card de busca nao apresenta labels persistentes | Depois de preenchido, o significado pode depender apenas do conteudo | Usar labels visiveis ou flutuantes |
| Baixa | Mapa mostra grande area fora do mercado principal | Parte da viewport tem pouco valor imediato | Ajustar enquadramento ao Brasil ou ao contexto do usuario |

## 8. Estados ainda necessarios para documentacao completa

- Carregamento do mapa e dos resultados.
- Permissao de geolocalizacao aceita, negada e indisponivel.
- Busca sem resultado.
- Busca com resultado e contador.
- Filtros abertos, aplicados e removidos.
- Marcador selecionado e card de resumo.
- Cluster de marcadores.
- Erro de mapa ou de rede.
- Layout responsivo para tablet e celular.
- Navegacao por teclado e foco visivel.

## 9. Fluxos inferidos

### Busca direta

1. Visitante informa localizacao.
2. Informa o servico desejado.
3. Opcionalmente abre filtros.
4. Aciona `Buscar`.
5. O mapa reposiciona e atualiza marcadores.
6. O visitante seleciona um resultado.
7. Abre o perfil ou inicia contato.

### Explorar pelo mapa

1. Visitante entra na pagina.
2. Examina os marcadores pre-carregados.
3. Seleciona um marcador SST ou Meio Ambiente.
4. Visualiza um resumo.
5. Abre os detalhes do prestador.

### Conversao

1. Visitante aciona `Comecar Agora`.
2. Inicia cadastro ou escolha de plano.

Os destinos e passos posteriores sao hipoteses funcionais; precisam ser validados com novos screenshots ou com a interface em execucao.

## 10. Criterios de aceitacao sugeridos

- O usuario consegue buscar usando apenas localizacao, apenas termo ou ambos.
- O sistema explica visual e textualmente o significado de cada categoria de marcador.
- Resultados no mapa possuem alternativa em lista e quantidade total.
- Marcadores sobrepostos sao agrupados em clusters.
- `Limpar busca` so fica ativo quando existe criterio ou filtro aplicado.
- Geolocalizacao informa claramente sucesso, negacao e erro.
- Todos os controles possuem foco visivel, nome acessivel e operacao por teclado.
- O estado sem resultados oferece orientacao para ampliar area ou remover filtros.

## 11. Evidencia utilizada

- Screenshot desktop fornecido pelo usuario em 2026-07-16.
- Nenhuma interacao, resposta de API ou destino de navegacao foi observado nesta analise.

## 12. Refinamento solicitado - legenda por cidade

Solicitacao registrada em 2026-07-16:

- Exibir uma legenda retangular ao lado dos marcadores.
- Usar o nome do profissional ou empresa como texto da legenda.
- Mostrar as legendas somente depois de uma busca por localizacao.
- Mostrar legenda apenas quando a cidade do resultado coincidir com a cidade pesquisada.
- Manter sem legenda os destaques nacionais exibidos no estado inicial.
- Manter marcadores de cidades alternativas sem legenda, mesmo quando retornados por fallback.

Proposta tecnica preservada em `.reversa/marker-labels-cidade-pesquisada.patch`.
