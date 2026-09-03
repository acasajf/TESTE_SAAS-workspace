# PRD - SST Finder

> Product Requirements Document gerado a partir da analise estatica do projeto, artefatos Reversa e documentacao existente.
> Data: 2026-05-22
> Status do produto: em desenvolvimento ativo

## 1. Visao do Produto

O SST Finder e uma plataforma SaaS brasileira para o mercado de Saude e Seguranca do Trabalho, Meio Ambiente, EPI e talentos tecnicos. O produto conecta empresas contratantes a profissionais, fornecedores e candidatos especializados, combinando marketplace, busca geografica, assinatura, gestao de propostas, dashboards operacionais e assistente de IA baseado em documentos tecnicos.

### Proposta de valor

- Para empresas: encontrar profissionais e fornecedores qualificados por localizacao, area e disponibilidade.
- Para profissionais: ganhar visibilidade, receber leads, gerenciar clientes, propostas, agenda, documentos e atividades.
- Para fornecedores de EPI: divulgar catalogo, produtos e servicos para um publico segmentado.
- Para candidatos: manter curriculo, certificacoes e portfolio em uma base especializada.
- Para administradores: gerir usuarios, contatos, assinaturas, comunicacoes e operacao da plataforma.

## 2. Problema

O mercado de SST e servicos ambientais e fragmentado. Empresas dependem de indicacoes, buscas manuais e contatos dispersos para encontrar profissionais confiaveis. Profissionais e fornecedores, por sua vez, carecem de um canal especializado para exposicao, captacao e gestao comercial.

## 3. Objetivos do Produto

| Objetivo | Descricao | Indicadores sugeridos |
| --- | --- | --- |
| Aumentar oferta qualificada | Cadastrar profissionais, empresas fornecedoras e candidatos | cadastros ativos, perfis completos |
| Gerar demanda | Facilitar busca e solicitacao de contato por empresas | leads criados, taxa de contato |
| Monetizar profissionais | Converter usuarios para planos pagos | MRR, conversao checkout, churn |
| Reduzir friccao comercial | Permitir envio e aceite de propostas por link publico | propostas enviadas, taxa de aceite |
| Aumentar retencao | Entregar dashboards, notificacoes, documentos e IA | MAU, recorrencia, uso do RAG |

## 4. Personas

### Profissional de SST ou Meio Ambiente

Precisa de visibilidade, organizacao comercial e ferramentas para gerenciar clientes, atividades, propostas, documentos e agenda.

### Empresa Contratante

Precisa encontrar profissionais confiaveis por especialidade e localizacao, comparar opcoes e iniciar contato rapidamente.

### Fornecedor de EPI

Precisa expor produtos, catalogos e dados comerciais para compradores do setor.

### Candidato Tecnico

Precisa criar um perfil profissional com curriculo, certificacoes e portfolio para oportunidades especializadas.

### Administrador da Plataforma

Precisa acompanhar usuarios, contatos, mensagens, assinaturas, relatorios e saude operacional do sistema.

## 5. Escopo do Produto

### Dentro do escopo atual

- Cadastro e autenticacao de usuarios.
- Perfis profissionais e empresariais.
- Busca de profissionais com filtros, localizacao e fallback geografico.
- Dashboards para profissional, empresa, candidato e administrador.
- Gestao de clientes, empresas, parceiros, documentos, agenda e financeiro.
- Sistema de propostas com link publico e resposta do cliente.
- Checkout e assinaturas via Stripe.
- Marketplace de fornecedores e produtos EPI.
- Modulo de talentos com curriculos, certificacoes e portfolio.
- Modulos operacionais de SST, ambiental e recursos.
- Chat RAG com base documental, Groq e processamento por fila.
- Comunicacoes por e-mail, SMS, WhatsApp/push quando configuradas.
- Upload de arquivos e servico auxiliar Python/FastAPI para arquivos pesados.

### Fora do escopo imediato

- Aplicativo mobile nativo.
- Chat em tempo real entre empresa e profissional.
- Pagamento transacional entre empresa e profissional dentro da plataforma.
- Marketplace de cursos.
- API publica para terceiros.
- Multi-idioma.

## 6. Requisitos Funcionais

### RF01 - Autenticacao e Usuarios

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF01.1 | Permitir cadastro de usuarios com roles distintas: admin, profissional, empresa e candidato | P0 |
| RF01.2 | Permitir login por e-mail e senha usando Auth.js/NextAuth | P0 |
| RF01.3 | Proteger rotas por sessao e role | P0 |
| RF01.4 | Permitir redefinicao de senha com token temporario | P0 |
| RF01.5 | Manter status de usuario: ativo, pendente, inativo ou suspenso | P0 |

### RF02 - Perfil Profissional

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF02.1 | Cadastrar dados profissionais, cidade, estado, telefone, bio e especialidades | P0 |
| RF02.2 | Permitir selecao de servicos conforme plano ou area de atuacao | P0 |
| RF02.3 | Permitir upload de avatar e curriculo | P1 |
| RF02.4 | Exibir perfil publico com especialidades, servicos, avaliacao e localizacao | P0 |
| RF02.5 | Registrar coordenadas para uso em busca e mapa | P1 |

### RF03 - Busca e Marketplace

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF03.1 | Buscar profissionais por texto, especialidade, cidade, estado e filtros avancados | P0 |
| RF03.2 | Executar fallback quando nao houver resultado na cidade exata | P0 |
| RF03.3 | Exibir resultados em lista e mapa | P1 |
| RF03.4 | Evitar sobreposicao visual de marcadores no mapa | P1 |
| RF03.5 | Permitir contato direto com profissional encontrado | P0 |

### RF04 - Planos, Checkout e Assinaturas

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF04.1 | Listar planos por area de atuacao | P0 |
| RF04.2 | Criar sessao de checkout no Stripe | P0 |
| RF04.3 | Validar webhook Stripe por assinatura | P0 |
| RF04.4 | Ativar usuario e assinatura apos pagamento confirmado | P0 |
| RF04.5 | Cancelar pendencias antigas ao confirmar uma nova assinatura | P1 |
| RF04.6 | Suportar ciclos mensal, trimestral, semestral, anual e bianual | P0 |

### RF05 - Propostas e Ordem Comercial

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF05.1 | Criar proposta com cliente, servicos, valores, prazo e condicoes | P0 |
| RF05.2 | Gerar link publico por token unico | P0 |
| RF05.3 | Permitir aceite ou recusa sem login | P0 |
| RF05.4 | Bloquear resposta de proposta expirada | P0 |
| RF05.5 | Atualizar o ServiceRequest relacionado quando a proposta for aceita | P0 |
| RF05.6 | Notificar o profissional sobre resposta do cliente | P1 |

### RF06 - Dashboard Profissional

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF06.1 | Exibir visao geral de clientes, atividades, notificacoes e financeiro | P0 |
| RF06.2 | Gerenciar clientes, empresas e parceiros | P0 |
| RF06.3 | Gerenciar agendamentos e atividades | P1 |
| RF06.4 | Gerenciar documentos profissionais | P1 |
| RF06.5 | Acompanhar notificacoes e leads recebidos | P0 |

### RF07 - Modulos SST, Ambiental e Recursos

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF07.1 | Gerenciar inspecoes, ocorrencias, riscos, EPIs e planos de acao | P0 |
| RF07.2 | Gerenciar avaliacoes quantitativas e adequacoes tecnicas | P1 |
| RF07.3 | Gerenciar licencas, residuos, emissoes e indicadores ESG | P1 |
| RF07.4 | Gerenciar equipamentos, EPCs e fornecedores tecnicos | P1 |

### RF08 - RAG e Base de Conhecimento

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF08.1 | Permitir ingestao de documentos PDF, texto ou markdown | P1 |
| RF08.2 | Processar documentos em background por fila | P1 |
| RF08.3 | Quebrar documentos em chunks e salvar embeddings | P1 |
| RF08.4 | Responder perguntas com base nos documentos recuperados | P1 |
| RF08.5 | Aplicar limite de 30 consultas por minuto por IP | P0 |

### RF09 - Admin

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF09.1 | Listar, filtrar e editar usuarios | P0 |
| RF09.2 | Gerenciar contatos e mensagens | P0 |
| RF09.3 | Visualizar relatorios e indicadores da plataforma | P1 |
| RF09.4 | Gerenciar financeiro administrativo com dados reais de assinaturas | P0 |
| RF09.5 | Enviar lembretes, SMS ou mensagens administrativas quando configurado | P1 |

## 7. Regras de Negocio

| ID | Regra | Prioridade |
| --- | --- | --- |
| RN01 | Assinaturas devem ser ativadas apenas apos confirmacao do webhook Stripe | P0 |
| RN02 | Eventos Stripe duplicados nao podem gerar assinaturas duplicadas | P0 |
| RN03 | Planos limitam quantidade de servicos ou especialidades disponiveis | P0 |
| RN04 | Busca sem resultado local deve expandir para estado, estados vizinhos e nivel nacional | P0 |
| RN05 | Marcadores de mapa com coordenadas iguais devem ser dispersos visualmente sem alterar o dado original | P1 |
| RN06 | Proposta vencida deve bloquear aceite/recusa publica | P0 |
| RN07 | Aceite de proposta deve atualizar tambem o pedido de servico vinculado | P0 |
| RN08 | RAG deve limitar abuso por IP | P0 |
| RN09 | Processamento de documentos deve ocorrer fora da requisicao principal | P1 |

## 8. Requisitos Nao Funcionais

| Categoria | Requisito |
| --- | --- |
| Performance | Busca deve retornar em tempo percebido baixo para listas e mapas |
| Confiabilidade | Webhooks financeiros precisam ser idempotentes |
| Seguranca | Senhas devem ser armazenadas com hash seguro |
| Seguranca | Rotas sensiveis devem validar sessao e role no servidor |
| Observabilidade | Erros criticos devem ser registrados em logger/monitoramento |
| Escalabilidade | Uploads e RAG devem usar workers ou servicos separados |
| Privacidade | Dados pessoais devem seguir boas praticas LGPD |
| Manutenibilidade | Dados mockados devem ser substituidos por APIs quando o modulo entrar em producao |

## 9. Arquitetura Resumida

| Camada | Tecnologia |
| --- | --- |
| Frontend e API | Next.js 15, React 19, App Router |
| UI | Tailwind CSS, Radix UI, lucide-react, Recharts |
| Banco | PostgreSQL com Prisma |
| Auth | Auth.js/NextAuth v5 |
| Pagamentos | Stripe Checkout e webhooks |
| IA | Groq via Vercel AI SDK |
| RAG | PostgreSQL/pgvector, Redis/Bull, worker Node |
| Upload pesado | FastAPI Python integrado ao MEGA |
| Comunicacao | E-mail, SMS, WhatsApp/push conforme configuracao |

## 10. Modelo de Dados Principal

Entidades centrais:

- User
- ProfessionalProfile
- CompanyProfile
- Plan
- Subscription
- ContactRequest
- MessageLog
- ServiceRequest
- Proposal
- Candidate, Resume, Certification, PortfolioItem
- EpiProduct e EpiProductImage
- RagDocument e RagChunk
- Modulos SST: Inspecao, Ocorrencia, RiscoOcupacional, EpiEstoque, PlanoAcao
- Modulos ambientais: LicencaAmbiental, ResiduoManifesto, EmissaoMonitoramento, IndicadorESG
- Modulos operacionais: Cliente, Empresa, Agendamento, TransacaoFinanceira, DocumentoProfissional, NotificacaoSistema

## 11. Fluxos Principais

### Cadastro de profissional

1. Usuario informa dados de acesso, localizacao, carreira, plano e servicos.
2. Sistema cria usuario e perfil profissional.
3. Sistema aplica limites de plano e ativa fluxo de checkout quando necessario.
4. Apos pagamento confirmado, webhook ativa usuario e assinatura.

### Busca de profissional

1. Empresa ou visitante informa termo, especialidade e local.
2. API busca profissionais ativos.
3. Se nao houver resultado local, sistema aplica fallback geografico.
4. Resultados aparecem em lista/mapa.
5. Usuario pode abrir perfil ou solicitar contato.

### Envio de proposta

1. Profissional cria proposta vinculada ou nao a um pedido de servico.
2. Sistema gera token publico.
3. Cliente acessa link sem login.
4. Cliente aceita ou recusa.
5. Sistema atualiza proposta, pedido vinculado e notificacoes.

### Consulta RAG

1. Usuario envia pergunta.
2. Sistema aplica rate limit.
3. Retriever busca chunks relevantes na base.
4. LLM responde em streaming com contexto recuperado.

## 12. Metricas de Sucesso

| Metrica | Objetivo |
| --- | --- |
| MRR | Medir receita recorrente |
| Conversao checkout | Medir eficiencia dos planos |
| Leads gerados | Medir valor do marketplace |
| Taxa de aceite de propostas | Medir qualidade do funil comercial |
| Perfis completos | Medir qualidade da oferta |
| Uso do RAG | Medir aderencia da IA |
| Churn | Medir retencao |
| Tempo de resposta da busca | Medir experiencia do usuario |

## 13. Riscos e Lacunas

| Risco/Lacuna | Impacto | Recomendacao |
| --- | --- | --- |
| Uso de mock-data e localStorage em fluxos de dashboard | Dados inconsistentes entre dispositivos | Migrar para APIs e banco |
| Financeiro admin parcialmente mockado | Indicadores administrativos podem nao refletir receita real | Integrar com Plan, Subscription e Stripe |
| Autorizacao dispersa por rotas | Risco de acesso indevido | Auditar todas as rotas de escrita |
| Dependencia de servicos externos | Indisponibilidade de Stripe, Groq, Redis, MEGA ou e-mail afeta produto | Criar degrade gracioso e alertas |
| Crescimento do schema Prisma | Dificuldade de manutencao | Organizar dominios e ownership por modulo |
| RAG com custo e latencia externos | Limites ou custos imprevisiveis | Monitorar uso, cachear e limitar por plano |

## 14. Roadmap Recomendado

### Fase 1 - Estabilizacao

- Auditar autorizacao das APIs.
- Remover ou isolar fluxos mockados em ambiente de producao.
- Integrar financeiro admin aos dados reais.
- Validar fluxo completo Stripe em ambiente local e producao.

### Fase 2 - Produto Comercial

- Melhorar funil de propostas e leads.
- Criar indicadores de conversao no dashboard admin.
- Adicionar trilha de auditoria para acoes administrativas.
- Consolidar notificacoes em um modulo unico.

### Fase 3 - Escala e Inteligencia

- Separar prioridades de fila por plano.
- Medir uso e qualidade do RAG.
- Criar recomendacao de profissionais por score.
- Evoluir marketplace de EPI e talentos.

## 15. Criterios de Aceite Globais

- Usuario profissional consegue se cadastrar, escolher plano e ficar ativo apos pagamento confirmado.
- Empresa consegue buscar profissionais e solicitar contato.
- Profissional consegue criar e enviar proposta publica.
- Cliente consegue responder proposta sem login.
- Administrador consegue visualizar e gerir usuarios e contatos.
- RAG responde apenas apos validar limite de uso e recuperar contexto.
- Rotas sensiveis validam permissao no servidor.
- Dados mockados nao sao usados como fonte de verdade em producao.

