# Dicionário de Dados — SST Finder

> Gerado pelo Archaeologist em 2026-05-02

## 1. Módulo: Admin

### Entidade: User (Contexto Admin) 🟢
Representa os usuários do sistema vistos pelo painel administrativo.

| Campo | Tipo | Descrição | Opcional |
| :--- | :--- | :--- | :--- |
| `id` | UUID | Identificador único do usuário | Não |
| `name` | String | Nome completo | Não |
| `email` | String | E-mail de login | Não |
| `role` | Enum | Papel no sistema (SUPER_ADMIN, ADMIN, etc) | Não |
| `status` | Enum | Situação (ACTIVE, PENDING, INACTIVE, SUSPENDED) | Não |
| `createdAt` | DateTime | Data de registro | Não |
| `professionalProfile` | Object | Dados de perfil se o usuário for um profissional | Sim |
| `companyProfile` | Object | Dados de perfil se o usuário for uma empresa | Sim |

### Entidade: RegistrationPlan 🟢
Representa as ofertas de planos disponíveis no momento do cadastro.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `name` | String | Nome do plano (BASIC, STANDARD, PRO, ENTERPRISE) |
| `price` | Decimal | Valor mensal |
| `limit` | Int | Limite de serviços que podem ser listados |
| `features` | String[] | Lista de benefícios inclusos |

### Entidade: ProfessionalFormData 🟢
Estrutura de dados coletada durante o cadastro de profissionais.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `name` | String | Nome completo |
| `email` | String | E-mail (chave única) |
| `phone` | String | Telefone / WhatsApp |
| `city` | String | Cidade de atuação |
| `state` | String (UF) | Estado de atuação |
| `specialties` | String[] | Especialidades (Técnico, Engenheiro, etc.) |
| `services` | String[] | Lista de serviços selecionados (limitada pelo plano) |

### Entidade: FinanceClient (Mock) 🟡
Utilizada no protótipo do módulo financeiro.

| Campo | Tipo | Descrição | Valores Exemplo |
| :--- | :--- | :--- | :--- |
| `id` | String | ID do cliente | "1" |
| `name` | String | Nome da empresa ou profissional | "Construtora Horizonte" |
| `type` | String | Categoria | "Empresa", "Profissional" |
| `plan` | String | Plano de assinatura | "ENTERPRISE", "PRO", "BASIC" |
| `status` | String | Status de pagamento | "Ativo", "Pendente", "Inativo" |
| `lastPayment` | String | Data do último pagamento | "15/01/2026" |
| `amount` | String | Valor da mensalidade | "R$ 850,00" |

### Entidade: SearchResult 🟢
Estrutura de dados retornada pela API de busca.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | ID do perfil profissional |
| `name` | String | Nome do profissional |
| `coordinates` | [Float, Float] | Latitude e Longitude [lat, lon] |
| `verified` | Boolean | Status de verificação do perfil |
| `rating` | Float | Avaliação média (default 5.0) |
| `reviewCount` | Int | Total de avaliações recebidas |
| `fallback` | Object | Metadados se o resultado não for da cidade original |

### Entidade: MapMarker 🟢
Objeto consumido pelo componente de mapa.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | String | ID único do marcador |
| `position` | [Float, Float] | Coordenadas geoespaciais |
| `type` | Enum | 'professional', 'search', 'talent' ou 'epi' |
| `title` | String | Título exibido no popup/hover |

### Entidade: Plan 🟢
Tabela estática que define os planos disponíveis no sistema.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | ID do plano |
| `name` | String | Nome do plano (Ex: PRO, ENTERPRISE) |
| `price` | Decimal | Preço base mensal |
| `area` | String | Área do plano (Ex: sst, curriculo, epi) |
| `serviceLimit` | Int | Limite de serviços permitidos |

### Entidade: Subscription 🟢
Vínculo entre o usuário e o plano que ele contratou.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | ID da assinatura interna |
| `userId` | String | FK para o Usuário |
| `planId` | String | FK para o Plano |
| `status` | Enum | PENDING, ACTIVE, CANCELLED |
| `billingCycle` | Enum | MENSAL, TRIMESTRAL, SEMESTRAL, etc. |
| `currentPeriodEnd` | DateTime | Data de expiração/renovação do acesso |
| `stripeCustomerId` | String | ID do cliente no Stripe |
| `stripeSubscriptionId`| String | ID da assinatura no Stripe (Chave Única) |

### Entidade: ServiceRequest 🟢
Armazena requisições de serviço diretas (orçamentos) para o profissional.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | ID da requisição |
| `userId` | String | FK para o Profissional que recebeu a requisição |
| `clientRazao` | String | Razão Social do cliente |
| `servicos` | String[] | Serviços solicitados |
| `status` | String | Estado do serviço (Proposta, Aceita, etc) |
| `valor` | String | Valor orçado |
| `createdAt` | DateTime | Data de criação |

### Entidade: Proposal 🟢
Orçamentos ou propostas enviadas pelo profissional.

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | ID da proposta |
| `userId` | String | FK para o Profissional que gerou a proposta |
| `clientEmail`| String | Email do cliente alvo |
| `servicos` | String[] | Serviços contemplados na proposta |
| `status` | String | enviada, aceita, recusada, expirada |
| `respondedAt`| DateTime | Quando o cliente respondeu à proposta |

---
