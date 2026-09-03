# Fluxograma: Função `POST /api/admin/users/[id]/sms`

Esta função processa o envio de SMS por um administrador para um usuário específico.

```mermaid
flowchart TD
    Start([Início]) --> Auth[Verifica Autenticação auth]
    Auth -- Não Autenticado --> E401[/401 Não autenticado/]
    Auth -- Autenticado --> CheckRole{Role Autorizada?}
    
    CheckRole -- Não --> E403[/403 Não autorizado/]
    CheckRole -- Sim --> Parse[Extrai id e message]
    
    Parse --> ValMsg{Mensagem Válida?}
    ValMsg -- Não --> E400_Msg[/400 Mensagem obrigatória ou > 160 chars/]
    
    ValMsg -- Sim --> Config{SMS Configurado?}
    Config -- Não --> E503[/503 Serviço não configurado/]
    
    Config -- Sim --> DB[Busca Usuário no Prisma]
    DB -- Não Encontrado --> E404[/404 Usuário não encontrado/]
    
    DB -- Encontrado --> Phone{Possui Telefone?}
    Phone -- Não --> E400_Phone[/400 Telefone não cadastrado/]
    
    Phone -- Sim --> Send[Executa sendSms]
    Send --> Result{Sucesso?}
    
    Result -- Não --> LogErr[Loga Erro]
    LogErr --> E500[/500 Falha ao enviar SMS/]
    
    Result -- Sim --> LogInfo[Loga Sucesso]
    LogInfo --> S200[/200 Sucesso com messageId/]
    
    S200 --> End([Fim])
    E401 --> End
    E403 --> End
    E400_Msg --> End
    E503 --> End
    E404 --> End
    E400_Phone --> End
    E500 --> End
```

### Regras de Negócio Identificadas 🟢
1.  **Autorização Estrita**: Somente ADMIN, SUPER_ADMIN ou SUPPORT podem disparar SMS.
2.  **Limite de Caracteres**: Máximo de 160 caracteres (padrão SMS single-segment).
3.  **Fallback de Telefone**: Prioriza telefone do perfil Profissional, depois Empresa.
4.  **Logging**: Todo envio bem-sucedido é logado com o ID do administrador responsável.
