# Análise Técnica do Código — SST Finder

> Gerado pelo Archaeologist em 2026-05-02
> Escala de Confiança: 🟢 CONFIRMADO | 🟡 INFERIDO | 🔴 LACUNA

---

## 1. Módulo: Admin
O módulo administrativo é o painel de controle central da plataforma, projetado com uma interface de dashboard moderna (estilo "dark/tech").

### Visão Geral Técnica
- **Arquitetura**: Componentes Next.js Client-side (`'use client'`) integrados a API Routes.
- **Estado**: Gerenciado localmente via hooks `useState` e `useMemo`.
- **Navegação**: Centralizada no `AdminSidebar` com controle de permissões por roles.

### Funcionalidades e Lógica de Negócio
#### 1.1 Gestão de Usuários 🟢
- **Lógica**: Interface para busca e filtragem dinâmica de usuários.
- **Integrações**: 
    - `GET /api/users`: Listagem paginada com filtros.
    - `PATCH /api/users/[id]`: Alteração de status (ACTIVE, INACTIVE, etc).
    - `POST /api/admin/users/[id]/sms`: Disparo de notificações SMS.
    - `POST /api/push/send`: Disparo de notificações WebPush.
- **Regras**: O sistema redireciona para perfis específicos conforme a role (`PROFESSIONAL` ou `COMPANY`).

#### 1.2 Financeiro 🟡
- **Estado Atual**: Atualmente implementado como um **protótipo com dados mockados** (`mock-data.ts`).
- **Capacidades**: Visualização de assinaturas, planos (BASIC, PRO, ENTERPRISE) e histórico de pagamentos.
- **Lacuna**: Falta integração com os modelos `Subscription` e `Plan` do Prisma existentes no backend.

#### 1.3 Dashboard e Relatórios 🟡
- **Componentes**: Utiliza Recharts para gráficos de crescimento, receita e saúde do sistema.
- **Lógica de Dados**: Agregações via hooks no diretório `_lib` (ex: `get-dashboard-stats.ts`).

### Algoritmos e Fluxos Complexos
- **Filtragem Combinada**: O `useMemo` é extensivamente usado para combinar busca textual, filtros de status/tipo e ordenação multi-campo sem requisições adicionais ao servidor (no caso do financeiro).
- **Controle de Acesso**: Baseado em roles injetadas via `useSession` (NextAuth), embora a filtragem no sidebar esteja comentada no código atual (🟡).

---

## 2. Módulo: Auth
Este módulo lida com a identidade, segurança e ciclo de vida de acesso dos usuários.

### Visão Geral Técnica
- **Motor de Autenticação**: Auth.js (NextAuth v5) com estratégia de sessão JWT.
- **Provedores**: Apenas `Credentials` (E-mail/Senha).
- **Segurança**: Hashing de senhas com `bcryptjs`.

### Funcionalidades e Lógica de Negócio
#### 2.1 Autenticação Baseada em Banco de Dados 🟢
- **Lógica**: O provedor busca o usuário no Prisma e compara o hash da senha.
- **Validação de Status**: Somente usuários com status `ACTIVE` ou `PENDING` podem efetuar login.
- **Persistência de Perfil**: Os perfis vinculados (`professionalProfile`, `companyProfile`) são carregados no ato do login e injetados no token JWT para disponibilidade global no cliente.

#### 2.2 Registro Multi-Step 🟢
- **Fluxo**: Processo de 5 etapas (Acesso -> Localização -> Carreira -> Plano -> Serviços).
- **Filtragem de Serviços**: A lista de serviços oferecidos pelo profissional é filtrada dinamicamente com base nas especialidades selecionadas (SST, Meio Ambiente ou Bombeiro Civil).
- **Gestão de Planos**: O cadastro impõe limites de seleção de serviços baseados no plano escolhido (BASIC: 13, STANDARD: 25, PRO: 40, ENTERPRISE: 100).
- **Auto-Login**: O sistema realiza o login automático do usuário imediatamente após a confirmação do cadastro bem-sucedido.

### Algoritmos e Fluxos Complexos
- **Normalização de Dados**: O e-mail é normalizado para lowercase antes de qualquer consulta ou inserção no banco para evitar duplicidade.
- **Hierarquia de Prefixos de Plano**: O sistema gera um prefixo dinâmico (`SST_` ou `MA_`) para o plano com base nas especialidades, garantindo que o usuário seja alocado na trilha de serviços correta.

---

## 3. Módulo: Buscar-Finder
O motor de busca é o coração do marketplace, conectando empresas a profissionais via geolocalização e filtros técnicos.

### Visão Geral Técnica
- **Arquitetura**: Frontend reativo (React hooks + MapLibre) sincronizado com uma API de busca performática.
- **Geocoding**: Híbrido entre dicionário local (`KNOWN_LOCATIONS`) e API Nominatim (OpenStreetMap).
- **Mapeamento**: Integração dinâmica com componentes de mapa para visualização espacial dos resultados.

### Funcionalidades e Lógica de Negócio
#### 3.1 Motor de Busca Multi-Camadas 🟢
- **Lógica de Fallback**: O sistema tenta encontrar resultados na cidade exata. Se falhar, expande automaticamente para o estado, estados vizinhos e, por fim, nível nacional.
- **Busca em Arrays**: Utiliza `unnest` do PostgreSQL para realizar buscas textuais eficientes dentro de campos do tipo array (`services` e `specialties`).
- **Filtros Avançados**:
    - `isEpiSupplier`: Filtra profissionais que prestam serviços relacionados a EPIs.
    - `hasCv`: Filtra apenas profissionais que possuem currículo cadastrado no Banco de Talentos.

#### 3.2 Visualização no Mapa 🟢
- **Algoritmo de Dispersão**: Implementa uma espiral baseada no Ângulo Dourado (137.5°) para espalhar marcadores que possuem a mesma coordenada de cidade, evitando sobreposição total.
- **Cálculo de Bounding Box**: O hook `useProfessionalSearch` calcula dinamicamente o centro e o nível de zoom ideais para englobar todos os resultados e o ponto de busca inicial.

### Algoritmos e Fluxos Complexos
- **Haversine Formula**: Utilizada para calcular a distância em quilômetros entre o centro da busca e os profissionais encontrados para fins de ordenação por proximidade (🟡 - inferido como base para melhorias futuras).
- **Normalização de Localidade**: Filtros de cidade sofrem limpeza de strings (regex) para remover sufixos de estado e garantir match com o banco de dados.

---

## 4. Módulo: Checkout e Planos (`checkout-planos`)
Responsável pela precificação, transações via Stripe e controle de assinaturas.

### Visão Geral Técnica
- **Arquitetura**: Pagamento via Stripe Checkout Sessions acoplado a um Webhook robusto para mudança de estados no banco.
- **Modelagem**: Tabelas `Plan` (estática, define a oferta) e `Subscription` (dinâmica, o vínculo do usuário ao plano).

### Funcionalidades e Lógica de Negócio
#### 4.1 Precificação Dinâmica (Descontos) 🟢
- O backend de checkout (API) calcula descontos de forma hardcoded com base no `billingCycle` escolhido para áreas padrão:
    - Trimestral: S/ Desconto
    - Semestral: 10% de Desconto
    - Anual: 20% de Desconto
    - Bianual: 25% de Desconto
- **Exceção**: Áreas especializadas (`curriculo`, `bombeiro-civil`, `epi`) não recebem esse desconto progressivo, o valor é apenas multiplicado pelos meses.

#### 4.2 Lógica de Webhook do Stripe 🟢
O endpoint do webhook é responsável por consolidar a transação:
- **`checkout.session.completed`**: Cancela inscrições `PENDING` antigas do usuário, cria a nova `Subscription`, ativa a conta do usuário (`UserStatus.ACTIVE`) e dispara os e-mails e SMS de boas vindas.
- **`invoice.payment_succeeded`**: Atualiza a data de vencimento da assinatura (`currentPeriodEnd`).
- **Falhas/Cancelamentos**: Cancela a assinatura (`SubscriptionStatus.CANCELLED`) em caso de falhas consecutivas (`invoice.payment_failed` ou `customer.subscription.deleted`).

### Algoritmos e Fluxos Complexos
- **Upsert Idempotente**: O webhook utiliza `prisma.subscription.upsert` baseado no `stripeSubscriptionId` para garantir que eventos duplicados do Stripe não criem assinaturas duplicadas no banco de dados.

---

## 5. Módulo: Dashboards (`dashboard-profissional-empresa`)
Interface central de controle, separada pelas personas de Profissional e Empresa.

### Visão Geral Técnica
- **Arquitetura**: Client-side rendering massivo (`use client`) com extensivo uso de **Lazy Loading** (`next/dynamic`) para componentes pesados como Modais e Calendário, visando otimização de bundle e LCP.
- **Integração de Estado**: Mistura de estado via banco de dados (perfis, notificações) e persistência local (`localStorage`) para simulação de comportamento onde o backend de listagem de jobs não está 100% completo (especialmente no dashboard de empresa).

### Funcionalidades e Lógica de Negócio
#### 5.1 Dashboard do Profissional (`/profissional/dashboard`) 🟢
- **Carregamento de Perfil**: Busca os dados de `/api/professionals/[id]` ou `session.user`.
- **Motor de Notificações**: Combina leads (buscas) (`ContactRequest`) com respostas de propostas (`Proposal`) num único feed de notificações centralizado no sino.
- **Gestão de Serviços**: Mostra uma tabela de serviços (`JobsTable`), que ao clicar permite gerar OS (`Ordem de Serviço`) com redirecionamento de ID.

#### 5.2 Dashboard da Empresa (`/empresa/dashboard`) 🟡
- **Fallback de Demo**: Caso não haja dados reais, renderiza dados predefinidos (`DEMO_USERS`) ou carrega de `localStorage`.
- **Gamificação de Perfil**: Exibe uma barra de progresso do perfil (0 a 100%) baseado no preenchimento de campos obrigatórios (CNPJ, Telefone, Cidade).
- **Upload de Catálogo**: Funcionalidade nativa para fornecedores de EPIs subirem PDF de catálogos via FormData (`/api/companies/catalog`).

### Algoritmos e Fluxos Complexos
- **Conversão de Lead em Serviço**: Ao "aceitar" uma notificação de lead vindo do módulo de busca, a UI injeta um novo registro na tabela de trabalhos e dispara um `PATCH` para a API marcando o `ContactRequest` como `CONVERTED`.
- **Integração ViaCEP**: O campo de perfil possui um listener de máscara nativo que ao preencher 8 dígitos aciona automaticamente o `viacep.com.br` para preenchimento geográfico automático.

---

## 6. Módulo: Gestão de Propostas (`proposals`)
Módulo voltado à formalização de orçamentos, aprovação do cliente via interface pública e notificação de callbacks ao profissional.

### Visão Geral Técnica
- **Arquitetura**: Utiliza "Magic Links" via UUID (`token`). A interface `/proposal/:token` é totalmente isolada da sessão, permitindo que o cliente externo acesse a proposta pelo celular/computador através do e-mail recebido, sem precisar de senha ou login.
- **Integração de Mensageria**: Comunicação dupla via `nodemailer` (envio do HTML da proposta) e SMS (notificações push de aceite ou recusa via webhook/provider local).

### Funcionalidades e Lógica de Negócio
#### 6.1 Envio de Proposta 🟢
- Profissional dispara o endpoint (`POST /api/proposals/send`).
- O sistema faz um "Upsert": cria uma nova `Proposal` com um novo `token` UUID. Se essa proposta for vinculada a uma requisição existente (`ServiceRequest`), ele atualiza os dados invés de criar do zero (útil para edição/reenvio de orçamento).
- Dispara e-mail com design responsivo (`HTML/CSS inline`) contendo o link da proposta pública.

#### 6.2 Resposta do Cliente (Pública) 🟢
- O endpoint (`POST /api/proposals/respond`) consolida a ação de aceite ou recusa.
- **Aceite**: Atualiza `Proposal` para `em_andamento` e o `ServiceRequest` vinculado para `aceita`.
- **Expirada**: Caso o `expiresAt` seja ultrapassado, trava o endpoint retornando HTTP 410.
- **Callbacks ao Profissional**: Emite um E-mail e tenta enviar um SMS avisando o profissional imediatamente que a proposta foi avaliada pelo cliente.

### Algoritmos e Fluxos Complexos
- **Segurança de Ação Única**: Endpoint de resposta valida se `proposal.status !== 'enviada'` na entrada (linha 38). Isso impede que propostas respondidas ou expiradas sejam "re-respondidas" através de requisições paralelas.

---

## 7. Módulo: Serviço Assíncrono (`mega_service-python`)
Microsserviço Python construído com FastAPI para desacoplar tarefas pesadas (upload de arquivos) do backend principal em Next.js.

### Visão Geral Técnica
- **Arquitetura**: Aplicação FastAPI independente com CORS habilitado, projetada para rodar paralelamente ao Node.js.
- **Armazenamento em Nuvem (PaaS)**: Integra-se com a API não-oficial do MEGA.nz (`mega.py`) para utilizar armazenamento gratuito na nuvem ao invés de hospedar os arquivos enviados por clientes (ex: Laudos antigos) no banco de dados ou no servidor do Next.js.

### Funcionalidades e Lógica de Negócio
#### 7.1 Processamento em Background 🟢
- Utiliza a classe `BackgroundTasks` nativa do FastAPI.
- A rota HTTP `POST /api/enviar-solicitacao` recebe os dados (Nome, Telefones e um UploadFile), salva o arquivo localmente como `temp_{nome_limpo}` e retorna HTTP 200 (sucesso) para o frontend imediatamente, em milissegundos.
- Em segundo plano (`processar_lead_completo`), o serviço:
  1. Conecta no Mega com as credenciais de ambiente.
  2. Sobe o arquivo e gera um link público.
  3. Remove o arquivo temporário local para liberar disco.
  4. Formata a mensagem com o link para ser disparada por WhatsApp e E-mail (atualmente o disparo final encontra-se em modo simulado/comentado).

### Algoritmos e Fluxos Complexos
- **Offloading de Processamento**: Delegação do tráfego IO-Bound longo (Upload de rede pro MEGA) da thread principal via `BackgroundTasks` protege o frontend de *timeout* (especialmente em ambientes Serverless como Vercel que matam a requisição após ~10 a 60 segundos).

---

## 8. Módulo: Inteligência Artificial (RAG)
Módulo projetado para permitir que usuários consultem uma base de conhecimento (ex: Normas Regulamentadoras) utilizando LLMs alimentados por documentos da plataforma (Retrieval-Augmented Generation).

### Visão Geral Técnica
- **Motor de Inferência**: Utiliza a **Groq API** (provavelmente modelos LLaMA) para baixíssima latência na geração de respostas, integrado perfeitamente ao **Vercel AI SDK** (`streamText`).
- **Banco de Vetores**: Utiliza o próprio PostgreSQL com a extensão **pgvector**.
- **Processamento Assíncrono**: O processamento (chunking e embedding) de PDFs não é feito na requisição web, mas sim enviado para uma fila no **Redis (BullMQ)** para evitar timeout.

### Funcionalidades e Lógica de Negócio
#### 8.1 Ingestão de Conhecimento (`/api/rag/ingest`) 🟢
- Recebe arquivos (PDF, TXT, MD). Extrai texto de PDF usando `pdf2json` com timeout de 60s embutido.
- Cria o registro em `rag_documents` com status `PROCESSING` e despacha para a fila via `addDocumentToQueue()`.

#### 8.2 Consulta ao Oráculo (`/api/rag/ask`) 🟢
- **Rate Limit**: Possui mitigação de abuso com IP tracker limitando a 30 requisições por minuto (`checkRateLimit()`).
- O sistema converte a última mensagem do usuário em vetor, faz uma busca utilizando Cosine Distance (`<=>`) restrita à coleção solicitada.
- Retorna um stream em tempo real para o frontend.

### Algoritmos e Fluxos Complexos
- **Extração Tolerante a Falhas**: A promessa de extração de PDF engloba listeners explícitos (`pdfParser_dataError`) com timeouts manuais para impedir que processos zumbis de conversão de arquivos travem a Vercel.

---

## 9. Módulo: Worker de Filas (`notifications-worker` / `document-processor`)
Microsserviço Node.js que consome filas no Redis para processamento de alto custo computacional, evitando sobrecarregar as rotas da API principal.

### Visão Geral Técnica
- **Arquitetura**: Executado como um processo independente (`npm run worker`), inicializado por `src/workers/document-processor.ts`.
- **Motor de Filas**: Utiliza a biblioteca **Bull(MQ)** sustentada por um servidor **Redis**.
- **Resiliência**: Lida nativamente com Graceful Shutdown (`SIGTERM`/`SIGINT`), finalizando tarefas antes de fechar a conexão com o banco ou fila.

### Funcionalidades e Lógica de Negócio
#### 9.1 Processamento de Documentos RAG 🟢
- O Worker consome eventos disparados pela rota `/api/rag/ingest`.
- **Chunking Estratégico**: Carrega o conteúdo bruto e o divide em pedaços lógicos.
- **Batch Embedding**: Em vez de fazer uma requisição na API de IA por palavra, ele agrupa os *chunks* em lotes (batch size: 30) para maximizar o throughput na geração de *embeddings*.
- **Transação Banco-Vetor**: Insere diretamente no Postgres usando `$executeRawUnsafe` convertendo o array numérico para o tipo `vector` (`$5::vector`). Usa `$transaction` para garantir que um lote inteiro entre junto ou falhe junto.

### Algoritmos e Fluxos Complexos
- **Reporte de Progresso**: O sistema não é uma "caixa preta". O worker relata progresso incremental de volta à fila (`job.progress()`) de 10% a 100%, calculando o avanço conforme cada lote de 30 chunks é finalizado. Isso permite que a UI exiba barras de carregamento precisas para o usuário.

---
