# Arquitetura do Sistema — SST Finder (C4 Model)

> Gerado pelo Reversa Architect em 2026-05-02

## Nível 1: Contexto de Sistema

O diagrama abaixo apresenta uma visão holística das interações do sistema SST Finder com os atores principais (Profissional, Empresa) e provedores de serviços externos.

```mermaid
C4Context
    title Diagrama de Contexto: SST Finder Platform

    Person(pro, "Profissional SST", "Busca oportunidades e envia orçamentos.")
    Person(emp, "Empresa / Cliente", "Busca serviços e responde a propostas.")

    System(sstFinder, "SST Finder", "Plataforma central de conexão entre empresas de saúde e segurança do trabalho e profissionais qualificados.")

    System_Ext(stripe, "Stripe", "Gateway de pagamentos para assinaturas e planos (Webhooks).")
    System_Ext(mega, "MEGA.nz API", "PaaS para armazenamento em nuvem de laudos e arquivos pesados.")
    System_Ext(groq, "Groq AI", "Motor de inferência de LLM de baixíssima latência (RAG).")
    System_Ext(sms, "Twilio / SMS", "Provedor de disparo de mensagens SMS para notificações críticas.")
    System_Ext(viaCep, "ViaCEP", "API de geolocalização e autocomplete de CEP.")

    Rel(pro, sstFinder, "Gere perfil, atende buscas e consome RAG.")
    Rel(emp, sstFinder, "Realiza buscas no mapa, analisa orçamentos.")
    
    Rel(sstFinder, stripe, "Cria sessões de checkout", "HTTPS")
    Rel(stripe, sstFinder, "Notifica transações via Webhook", "HTTPS")
    
    Rel(sstFinder, mega, "Transfere arquivos pesados em background via FastAPI", "API")
    Rel(sstFinder, groq, "Pede respostas de inferência de linguagem", "REST / StreamText")
    Rel(sstFinder, sms, "Dispara callbacks de proposta", "REST")
    Rel(sstFinder, viaCep, "Consulta endereços no cadastro", "HTTPS")
```

## Nível 2: Diagrama de Containers

Visão arquitetural profunda detalhando os contêineres e aplicações separadas que mantêm a plataforma viva.

```mermaid
C4Container
    title Diagrama de Containers: SST Finder

    Person(user, "Usuários Finais")

    System_Boundary(c1, "Ecossistema SST Finder") {
        Container(nextjs, "Web Application", "Next.js 15 (React)", "Frontend Client-Side e Server Actions (SSR/RSC) integrados.")
        Container(apiRAG, "RAG & Chat API", "Next.js Route Handlers", "Filtro de requisições, Streaming SDK e proteção de Rate Limit.")
        
        Container(fastapi, "Mega Async Service", "Python (FastAPI)", "Microsserviço de Offloading para upload no MEGA.")
        Container(worker, "RAG Worker", "Node.js (BullMQ)", "Processa quebra de textos e embeddings isolado do servidor principal.")
        
        ContainerDb(pg, "PostgreSQL Master", "Relacional", "Banco de dados principal contendo perfis, assinaturas e vetores (pgvector).")
        ContainerDb(redis, "Redis", "Key-Value Store", "Gerenciamento da fila de processamento (BullMQ).")
    }

    Rel(user, nextjs, "Acessa via HTTPS", "Browser")
    Rel(nextjs, apiRAG, "Envia requisições de consulta AI", "Internal Fetch")
    
    Rel(nextjs, pg, "Leitura e Gravação síncrona", "Prisma ORM")
    Rel(apiRAG, pg, "Consultas Vectoriais (Cosine Dist)", "Prisma Raw SQL")
    
    Rel(nextjs, redis, "Enfileira documentos para processar", "BullMQ")
    Rel(worker, redis, "Consome filas em lote", "BullMQ")
    Rel(worker, pg, "Insere Embeddings", "SQL Raw")
    
    Rel(nextjs, fastapi, "Dispara arquivo para processamento longo", "REST POST")
```

## Relacionamentos de Dados (ERD Parcial de Alta Importância)

O esquema central que liga os profissionais às oportunidades financeiras e à IA.

```mermaid
erDiagram
    USER ||--o{ SUBSCRIPTION : "contrata"
    USER ||--o{ PROPOSAL : "envia"
    USER ||--o{ SERVICE_REQUEST : "recebe"
    
    SUBSCRIPTION }|--|| PLAN : "baseado em"
    PROPOSAL }|--o| SERVICE_REQUEST : "resolve (opcional)"
    
    RAG_DOCUMENT ||--o{ RAG_CHUNK : "dividido em"
```
