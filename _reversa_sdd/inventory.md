# Inventário do Projeto — SST Finder

> Gerado pelo Scout em 2026-05-02

## 1. Visão Geral
O **SST Finder** é uma plataforma completa para o setor de Saúde e Segurança do Trabalho (SST) no Brasil. É construída com uma arquitetura moderna baseada em Next.js 15, utilizando TypeScript como linguagem principal e integrando serviços auxiliares em Python.

## 2. Estrutura de Pastas (Simplificada)
- `src/app/` — Rotas e interface da aplicação (Next.js App Router)
- `src/components/` — Componentes UI reutilizáveis (Radix UI, Tailwind)
- `src/services/` — Lógica de serviços (City, EPI, File Upload, Talent)
- `src/lib/` — Utilitários, configurações de DB, regras de negócio e integrações (Stripe, RAG, Geo)
- `src/workers/` — Processamento em segundo plano (Bull/Redis)
- `mega_service/` — Serviço auxiliar em Python (Provavelmente para processamento pesado de dados ou IA)
- `prisma/` — Modelagem de banco de dados e migrações
- `scripts/` — Scripts utilitários de manutenção e seed

## 3. Módulos Identificados
1.  **Módulo Administrativo (`/admin`)**: Gestão global da plataforma.
2.  **Autenticação (`src/auth.ts`)**: Baseado em NextAuth.
3.  **Busca e Finder (`/buscar`, `/finder`)**: Core do sistema para encontrar profissionais e empresas.
4.  **Checkout e Planos (`/checkout`, `/planos`)**: Monetização e integração com Stripe.
5.  **Dashboards (`/profissional`, `/empresa`, `/talent`)**: Áreas logadas para diferentes perfis de usuários.
6.  **Propostas (`/proposal`)**: Sistema de geração e gestão de propostas comerciais de SST.
7.  **Processamento de Documentos/RAG**: Sistema de IA para análise de documentos técnicos.
8.  **Notificações e Workers**: Envio de WhatsApp, Email e WebPush via workers Bull.

## 4. Tecnologias Principais
- **Frontend/Backend**: Next.js 15 (React 19)
- **Banco de Dados**: PostgreSQL com Prisma ORM
- **Processamento**: Python (mega_service) + Bull/Redis (workers)
- **Integrações**: Stripe (Pagamentos), Supabase (Storage/DB), Vercel (Insights)
- **IA**: Groq, OpenAI, AI SDK

## 5. Pontos de Entrada
- **Web**: `src/app/layout.tsx` -> `src/app/page.tsx`
- **Worker**: `src/workers/document-processor.ts`
- **Python Service**: `mega_service/main.py`
