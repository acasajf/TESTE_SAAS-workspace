# Fluxograma: Dashboard Profissional e Empresa

## Arquitetura de Dashboards (Client-Side Rendering)

```mermaid
graph TD
    UI[Dashboard Page (React Client)] --> Session{Possui Sessão?}
    Session -- Não --> Redir[/Redireciona para /entrar/]
    Session -- Sim --> DataFetch[Fetch Dados Paralelos]

    DataFetch --> FetchProfile[GET /api/(professionals|companies)/:id]
    DataFetch --> FetchReq[GET /api/profissional/service-requests]
    DataFetch --> FetchNotif[GET /api/profissional/notifications]

    FetchProfile --> SetState[Atualiza Estado Local]
    FetchReq --> SetState
    FetchNotif --> SetState

    SetState --> Render[Renderiza Interface Principal]
    Render --> Lazy[Lazy Load Modais e Calendário]

    Lazy -.-> |Usuário clica em 'Editar Perfil'| ModalProfile[ProfileEditorModal]
    ModalProfile --> Save[PUT /api/users/:id]
    Save --> UpdateDB[Prisma: Atualiza User/Profile]
    UpdateDB --> Reload[Recarrega Dados]
```

## Fluxo de Notificações e Oportunidades

```mermaid
sequenceDiagram
    participant P as Profissional
    participant DB as Prisma
    participant API as API Notifications

    API->>DB: Busca ContactRequest (Leads)
    DB-->>API: Retorna Leads não convertidos
    
    API->>DB: Busca Proposal (Orçamentos)
    DB-->>API: Retorna Propostas Respondidas (Aceita/Recusada)
    
    API-->>P: Array Combinado de Notificações

    P->>P: Visualiza Alerta na UI
    
    alt Aceitou Lead
        P->>API: PATCH /api/profissional/notifications (status: CONVERTED)
        API->>DB: Marca Lead como Convertido
        P->>P: Cria novo Serviço/Job na UI local
    end
```
