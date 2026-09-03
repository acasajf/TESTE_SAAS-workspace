# Software Design Document (SDD) — SST Finder

> Gerado pelo Reversa Writer em 2026-05-02
> Fase 4: Geração de Especificações Executáveis

## 1. Visão Geral do Sistema
O **SST Finder** é um SaaS focado no nicho de Saúde e Segurança do Trabalho (SST). Ele conecta empresas (contratantes) a profissionais qualificados da área. O sistema oferece:
*   Busca geolocalizada de profissionais.
*   Assinatura via Stripe para liberação de perfil profissional.
*   Gestão de Orçamentos/Propostas através de links mágicos (públicos).
*   Chat com Inteligência Artificial baseado nos próprios documentos da área (RAG) utilizando Vercel AI SDK e Groq.
*   Upload Assíncrono de documentos pesados utilizando FASTAPI integrado à nuvem Mega.nz.

## 2. Pilha Tecnológica
*   **Frontend & API Principal**: Next.js 15 (React), Tailwind CSS, Lucide React.
*   **Banco de Dados**: PostgreSQL (usando `pgvector` para similaridade vetorial) e Redis (fila).
*   **ORM**: Prisma ORM.
*   **Microsserviços**: 
    *   `mega_service-python` (FastAPI / Python) para upload pesado.
    *   `notifications-worker` (Node.js / BullMQ) para RAG embeddings.
*   **Integrações**: Stripe (Pagamentos), Groq (LLM), MEGA.nz (Armazenamento), ViaCEP (Geolocalização), Nodemailer e Twilio (Notificações).

## 3. Épicos e User Stories (Principais)

### Épico 1: Encontro e Contratação
*   **US-01 (Busca)**: Como Empresa, quero buscar profissionais por especialidade e local, para encontrar o melhor técnico próximo à minha obra. *(O sistema usará fallback de raio caso não encontre na cidade e aplicará Golden Angle para evitar pin-collision)*.
*   **US-02 (Orçamento)**: Como Profissional, quero gerar um link de orçamento, para enviá-lo pelo WhatsApp sem que a empresa precise se cadastrar no site para aceitá-lo. *(O sistema gera um UUID public token)*.

### Épico 2: Monetização
*   **US-03 (Assinatura)**: Como Profissional, quero pagar uma assinatura via PIX/Cartão, para que meu perfil fique visível nas buscas. *(O sistema utiliza o Stripe Checkout e libera as funcionalidades de acordo com a trava de limite de serviços do plano)*.

### Épico 3: IA e Documentação
*   **US-04 (Chat de Normas)**: Como Profissional, quero conversar com a IA sobre Normas Regulamentadoras, para tirar dúvidas técnicas na obra. *(O sistema restringe a 30 msg/min via IP e usa RAG para recuperar contexto exato)*.

## 4. Contratos Operacionais Críticos (APIs)

### 4.1 Rota de RAG (Busca e Geração)
*   **Endpoint**: `POST /api/rag/ask`
*   **Input**: `messages[]` (histórico) e `collection` (NRs).
*   **Regras**: Valida rate limit. Extrai Embedding de `messages[-1]`. Busca no PG usando `<=>` limitando ao top K. Concatena contexto e chama `streamText` usando Groq.
*   **Retorno**: `TextStream` progressivo.

### 4.2 Rota de Webhook Stripe
*   **Endpoint**: `POST /api/webhooks/stripe`
*   **Input**: Payload assinado criptograficamente pelo Stripe.
*   **Regras**: Valida `Stripe-Signature`. Lê evento (ex: `checkout.session.completed`). Busca plano interno correspondente usando mapeamento predefinido. Executa `prisma.subscription.upsert` focado em `stripeSubscriptionId` (Garantia de Idempotência).

## 5. Code-Spec Matrix (Onde o código vive)

| Módulo Logico | Caminho Principal no Código | Status de Saúde Reversa |
| :--- | :--- | :--- |
| **Auth** | `src/auth.ts`, `src/app/api/auth` | Excelente |
| **Buscador Geográfico** | `src/app/api/search/professionals` | Bom (Usa Raw SQL complexo) |
| **Dashboards CSR** | `src/app/profissional/dashboard` | Atenção (Forte uso de Lazy Load) |
| **Worker Assíncrono** | `src/workers/document-processor.ts`| Excelente (Resiliente e Em Lote) |
| **Mega Serveless** | `mega_service/main.py` | Bom (Processamento off-thread) |

## 6. Riscos e Recomendações
1.  **Riscos de Estado Híbrido**: No Dashboard da Empresa existem vestígios de estado sendo salvos em `localStorage`. É recomendável que no futuro isso seja centralizado na API para garantir sincronicidade cross-device.
2.  **Segurança em Webhooks**: O Webhook do Stripe está seguro e bem documentado. É recomendável garantir que a máquina Python seja roteada através de uma VPN fechada ou autenticada por Header fixo para impedir que usuários batam direto no `mega_service`.
