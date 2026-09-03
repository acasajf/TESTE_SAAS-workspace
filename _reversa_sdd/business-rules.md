# Regras de Negócio e Impactos — SST Finder

> Extraídas pelo Reversa Detective em 2026-05-02

## 1. Regras de Precificação e Assinatura (Checkout)
*   **BR-001 (Ciclo de Cobrança)**: Assinaturas possuem 4 ciclos - Mensal, Trimestral, Semestral e Anual. O desconto máximo (Anual) chega a 25% no backend e é gerado via Stripe Prices na API `/api/checkout`.
*   **BR-002 (Idempotência de Webhook)**: O sistema não deve assinar usuários duplicados em caso de falha temporária do Stripe. A API valida `stripeSubscriptionId` e recusa transações duplicadas de criação via `upsert`.
*   **BR-003 (Limites de Serviços)**: Profissionais podem cadastrar diferentes quantidades de serviços e especialidades, baseados diretamente no plano contratado (BASIC = 3, ENTERPRISE = 15).

## 2. Regras do Motor de Busca
*   **BR-004 (Fallback Geográfico)**: Se uma busca por "Engenheiro de Segurança" não retornar resultados na Cidade/Estado exatos, a API expande a busca omitindo o filtro geográfico, mantendo a relevância baseada em geolocalização bruta.
*   **BR-005 (Dispersão de Mapa)**: Para evitar colisão visual de marcadores de profissionais que moram na mesma rua, a API aplica um algoritmo baseado em *Golden Angle* (137.5 graus) para espalhar os pins visualmente sem alterar o dado do banco.

## 3. Gestão de Propostas e Orçamentos
*   **BR-006 (Propostas Expiráveis)**: Uma proposta `Proposal` com data `expiresAt` vencida bloqueia a rota de aceite público (`/api/proposals/respond`), emitindo HTTP 410.
*   **BR-007 (Efeito Cascata do Aceite)**: Aceitar uma proposta (`Proposal`) não apenas a marca como "em andamento", mas implicitamente converte o `ServiceRequest` associado para status "aceito", completando o funil de lead.

## 4. Governança e Processamento RAG (Inteligência Artificial)
*   **BR-008 (Anti-Abuso RAG)**: Consultas na base de conhecimento (chat RAG) são estritamente limitadas a 30 por minuto por endereço de IP.
*   **BR-009 (Ingestão Tolerante)**: O parseamento de PDFs complexos na ingestão RAG cancela automaticamente via timeout aos 60 segundos para evitar *memory leaks* ou travamento de Workers *Serverless*.
*   **BR-010 (Batching Safes)**: O *Document Processor* nunca envia todo o arquivo simultaneamente para a API Groq. Ele trabalha em lotes de no máximo 30 *chunks*, salvos atomicamente no PostgreSQL usando transações (`$transaction`).

## Matriz de Impacto (Spec Impact Matrix)

| Ação do Usuário | Módulos Impactados | Efeito Cascata | Risco |
| :--- | :--- | :--- | :--- |
| **Pagar Plano no Checkout** | `checkout`, `admin`, `stripe` | Webhook recebe pagamento -> Atualiza status no User Profile -> Altera limites no Frontend. | Médio (Downtime Stripe) |
| **Aceitar Proposta por E-mail** | `proposals`, `dashboards` | Atualiza Proposal -> Atualiza ServiceRequest -> Dispara E-mail -> Dispara SMS. | Alto (Inconsistência de State) |
| **Fazer Upload de Laudo (Mega)** | `mega_service`, `dashboards` | Frontend libera fluxo -> FastAPI Background salva arquivo -> Gera Link Mega -> Notifica Admin. | Baixo |
| **Subir PDF para RAG** | `rag-ai`, `worker`, `db` | PDF validado -> Salvo local temporário -> Dispara Job (Redis) -> Worker quebra PDF -> Grava Vetor -> Atualiza Status RAG. | Médio (Crash de Worker) |
