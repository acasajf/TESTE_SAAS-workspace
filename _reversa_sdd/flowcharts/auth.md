# Fluxogramas: Módulo Auth

## Fluxo de Autenticação (Login)

```mermaid
sequenceDiagram
    participant User as Usuário
    participant Client as Next.js (Client)
    participant Auth as NextAuth (Server)
    participant DB as Prisma (PostgreSQL)

    User->>Client: Insere E-mail e Senha
    Client->>Auth: signIn('credentials', {email, password})
    Auth->>DB: findUnique({ email: lowercase })
    DB-->>Auth: Retorna User + Profiles
    
    alt Usuário não encontrado ou Senha incorreta
        Auth-->>Client: Retorna erro de credenciais
        Client-->>User: Exibe "Credenciais inválidas"
    else Usuário Inativo ou Suspenso
        Auth-->>Client: Retorna erro de status
        Client-->>User: Exibe "Conta não permitida"
    else Sucesso
        Auth->>Auth: bcrypt.compare()
        Auth->>Auth: Gera Token JWT com Roles e Dados
        Auth-->>Client: Retorna Sucesso
        Client->>User: Redireciona para Dashboard
    end
```

## Middleware de Proteção de Rotas

```mermaid
graph TD
    Request[Requisição para /rota] --> Public{É Rota Pública?}
    Public -- Sim --> Allow[Permite Acesso]
    Public -- Não --> Authed{Usuário Logado?}
    
    Authed -- Não --> Login[Redireciona para /entrar]
    Authed -- Sim --> Admin{Rota /admin?}
    
    Admin -- Não --> Allow
    Admin -- Sim --> RoleCheck{Role = ADMIN?}
    
    RoleCheck -- Sim --> Allow
    RoleCheck -- Não --> Forbidden[Redireciona para / (Home)]
```
