# Fluxograma Detalhado: Registro de Profissional

O registro é um processo de 5 etapas com validações intermediárias e auto-login final.

```mermaid
graph TD
    Step1[Etapa 1: Acesso] --> Val1{Válido?}
    Val1 -- Não --> Error1[Mostra Erro]
    Val1 -- Sim --> Step2[Etapa 2: Localização]
    
    Step2 --> Val2{Válido?}
    Val2 -- Não --> Error2[Mostra Erro]
    Val2 -- Sim --> Step3[Etapa 3: Carreira]
    
    Step3 --> Val3{Válido?}
    Val3 -- Não --> Error3[Mostra Erro]
    Val3 -- Sim --> Step4[Etapa 4: Plano]
    
    Step4 --> Val4{Plano Selecionado?}
    Val4 -- Não --> Error4[Mostra Erro]
    Val4 -- Sim --> Step5[Etapa 5: Serviços]
    
    Step5 --> Submit[Clique em Cadastrar]
    
    Submit --> Upload[Upload Avatar para /api/upload/avatar]
    Upload --> API[POST /api/register]
    
    API --> DB_User[Cria Usuário no DB]
    DB_User --> DB_Profile[Cria ProfessionalProfile]
    DB_Profile --> API_Res{Sucesso?}
    
    API_Res -- Erro --> ShowErr[Exibe Erro da API]
    API_Res -- Sucesso --> AutoLogin[signIn automático]
    
    AutoLogin --> FinalRedirect[Redireciona conforme plano/callback]
```

### Regras de Negócio do Cadastro 🟢
1.  **Validação de Senha**: Mínimo de 8 caracteres.
2.  **Unicidade**: O e-mail deve ser único (verificado na API).
3.  **Filtragem de Áreas**:
    - Se especialidade incluir "Ambiental" -> Prefixo de Plano **MA**.
    - Caso contrário -> Prefixo de Plano **SST**.
4.  **Limites do Plano**:
    - O frontend bloqueia seleções acima do limite (ex: 13 para BASIC).
    - A API valida se o `planName` enviado é coerente com o número de serviços.
