# Fluxo de UI - Kanban Comercial

## Fluxo publico - Finder no mapa

```mermaid
flowchart TD
    A["Entrada na pagina publica do SST Finder"] --> B["Mapa carregado com marcadores"]
    B --> C{"Acao do visitante"}
    C --> D["Informar localizacao"]
    C --> E["Informar servico ou termo"]
    C --> F["Abrir filtros"]
    C --> G["Usar localizacao atual"]
    C --> H["Selecionar marcador"]
    C --> I["Entrar"]
    C --> J["Comecar Agora"]
    D --> K["Buscar"]
    E --> K
    F --> K
    G --> K
    K --> L["Atualizar area e resultados do mapa"]
    H --> M["Exibir resumo do profissional ou empresa"]
    M --> N["Abrir perfil ou contato"]
    I --> O["Fluxo de autenticacao"]
    J --> P["Fluxo de cadastro ou adesao"]
```

Os passos apos "Buscar", "Selecionar marcador", "Entrar" e "Comecar Agora" sao inferidos porque o screenshot nao mostra os respectivos estados de destino.

---

```mermaid
flowchart TD
    A["Kanban Comercial"] --> B["Coluna: Novo Lead"]
    B --> C["Abrir ou criar card de lead"]
    C --> D["Modal: Novo Lead"]
    D --> E["Preencher dados basicos"]
    E --> F{"Acao escolhida"}
    F --> G["Salvar Lead"]
    F --> H["Iniciar Atendimento"]
    F --> I["Marcar como Perdido"]
    G --> B
    H --> J["Mover para Atendimento"]
    I --> K["Mover para Perdido"]
    J --> A
    K --> A
    A --> L["Coluna: Proposta"]
    L --> M["Modal: Proposta"]
    M --> N["Preencher escopo, valor, prazo, validade e pagamento"]
    N --> O["Salvar Proposta"]
    O --> P["Gerar PDF"]
    P --> Q["Enviar proposta ao cliente"]
    Q --> R["Atualizar status: Enviada ou Aguardando retorno"]
    R --> S{"Retorno do cliente"}
    S -->|Sem resposta| U["Manter em Proposta com follow-up"]
    S -->|Pediu ajuste/desconto/condicao| T["Enviar para Negociacao"]
    S -->|Aceitou sem ajuste| W["Enviar para Formalizacao"]
    S -->|Recusou| X["Marcar como Perdido"]
    S -->|Pediu nova versao| Y["Revisar proposta"]
    T --> V["Mover para Negociacao"]
    U --> M
    V --> A
    W --> A
    X --> A
    Y --> M
```

## Pontos de entrada

- Acesso direto ao Kanban Comercial.
- Acao sobre um card existente na coluna "Novo Lead".
- Possivel criacao de novo card a partir da etapa "Novo Lead".

## Pontos de saida

- Fechar modal sem acao visivel.
- Salvar lead permanecendo em "Novo Lead".
- Iniciar atendimento, avancando o lead para a etapa "Atendimento".
- Marcar como perdido, enviando o lead para a etapa "Perdido".
- Salvar proposta mantendo o card na etapa "Proposta".
- Gerar PDF da proposta.
- Enviar para negociacao quando a proposta estiver enviada/validada e houver retorno ou tratativa comercial.
- Manter em "Proposta" quando ainda for apenas rascunho ou aguardando envio.

## Regra recomendada para transicao Proposta -> Negociacao

O card deve ir para "Negociacao" quando a proposta ja foi formalizada para o cliente e existe uma conversa ativa sobre condicoes, ajustes, desconto, prazo, forma de pagamento ou aceite. Se o status interno ainda for "Rascunho", a acao correta e salvar ou gerar PDF, nao avancar.

## Regra recomendada para retorno do cliente

Depois de enviada, a proposta deve permanecer na etapa "Proposta" ate o retorno ser classificado. Sem resposta gera follow-up. Pedido de ajuste, desconto ou condicao leva para "Negociacao". Aceite sem ajustes leva para "Formalizacao". Recusa leva para "Perdido". Pedido de nova versao mantem em "Proposta" para revisao.
