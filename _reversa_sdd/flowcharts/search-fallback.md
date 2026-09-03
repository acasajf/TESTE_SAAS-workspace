# Fluxograma Detalhado: Lógica de Fallback de Busca

Esta lógica garante que o usuário nunca receba uma "página vazia" se houver profissionais disponíveis em regiões próximas.

```mermaid
flowchart TD
    Start([Início da Busca]) --> Exact[Busca por Cidade Exata]
    Exact --> Check1{Encontrou?}
    
    Check1 -- Sim --> Return[/Retorna Resultados Exatos/]
    Check1 -- Não --> State[Busca por Estado]
    
    State --> Check2{Encontrou?}
    Check2 -- Sim --> ReturnState[/Retorna Resultados do Estado + Info Fallback/]
    
    Check2 -- Não --> Neighbors{Possui Estados Vizinhos?}
    Neighbors -- Sim --> SearchNeighbors[Busca nos Estados Vizinhos mapeados]
    Neighbors -- Não --> National
    
    SearchNeighbors --> Check3{Encontrou?}
    Check3 -- Sim --> ReturnNeighbors[/Retorna Resultados Vizinhos + Info Fallback/]
    
    Check3 -- Não --> National[Busca em Nível Nacional - Sem filtro local]
    National --> Check4{Encontrou?}
    
    Check4 -- Sim --> ReturnNational[/Retorna Resultados Nacionais + Info Fallback/]
    Check4 -- Não --> AnyActive[Último Recurso: Busca qualquer Profissional Ativo]
    
    AnyActive --> Check5{Encontrou?}
    Check5 -- Sim --> ReturnAny[/Retorna Profissionais Aleatórios + Info Fallback/]
    Check5 -- Não --> Empty[/Retorna Lista Vazia/]
```

### Inteligência de Fallback 🟢
1.  **Dicionário de Vizinhos**: Utiliza o mapa `NEIGHBORING_STATES` (ex: MG faz fronteira com SP, RJ, ES, BA, GO, MS).
2.  **Transparência**: O backend retorna um objeto `fallbackInfo` explicando qual nível de busca foi atingido, permitindo que o frontend exiba mensagens como "Mostrando resultados de todo o Brasil".
3.  **Preservação de Filtros Técnicos**: Especialidade, Avaliação Mínima e Status de Verificação são preservados em todos os níveis de fallback geográfico.
