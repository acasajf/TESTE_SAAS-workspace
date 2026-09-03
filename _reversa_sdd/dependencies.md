# Dependências do Projeto — SST Finder

## 1. Dependências de Runtime (Principais)

| Pacote | Versão | Propósito |
| :--- | :--- | :--- |
| `next` | `^15.0.0` | Framework Fullstack |
| `react` | `19.2.0` | Biblioteca de UI |
| `@prisma/client` | `^5.10.0` | ORM para Banco de Dados |
| `stripe` | `^14.25.0` | Gateway de Pagamentos |
| `next-auth` | `^5.0.0-beta.30` | Autenticação |
| `ai` | `^6.0.79` | Vercel AI SDK |
| `@supabase/supabase-js`| `^2.104.0` | Integração com Supabase |
| `bull` | `^4.16.5` | Gerenciamento de Filas (Redis) |
| `tailwindcss` | `^3.4.19` | Framework de CSS |
| `zod` | `^4.3.5` | Validação de Esquemas |

## 2. Dependências de Desenvolvimento

| Pacote | Versão | Propósito |
| :--- | :--- | :--- |
| `typescript` | `^5` | Tipagem estática |
| `vitest` | `^4.0.16` | Framework de Testes Unitários |
| `@playwright/test` | `^1.58.2` | Testes E2E |
| `prisma` | `^5.10.0` | Ferramentas CLI do Prisma |
| `eslint` | `^9` | Linting de código |

## 3. Serviços Externos Detectados
- **Stripe**: Cobranças e assinaturas.
- **Supabase**: Banco de Dados e possivelmente Storage.
- **Resend / MailerSend**: Envio de e-mails transacionais.
- **OneSignal**: Notificações Push.
- **Google Maps / Mapbox / MapLibre**: Funcionalidades geoespaciais.
- **Groq / OpenAI**: Serviços de Inteligência Artificial.
