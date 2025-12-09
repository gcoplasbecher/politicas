# Variáveis de Ambiente Necessárias

## Última Atualização: [Inserir Data]

Este documento lista todas as variáveis de ambiente (env vars) essenciais para o funcionamento correto do aplicativo, especialmente para integrações com APIs da Meta (WhatsApp, Instagram e Graph API). As variáveis são carregadas a partir de um arquivo `.env` no diretório raiz do projeto.

**Arquivo Modelo**: Um `.env.example` foi gerado automaticamente com todas as variáveis necessárias. Copie para `.env` e preencha os valores reais.

## 1. Por Que Usar Variáveis de Ambiente?

- **Segurança**: Não armazenar credenciais no código-fonte, evitando exposição em repositórios públicos.
- **Configuração Flexível**: Facilita troca entre ambientes (desenvolvimento, produção).
- **Padronização**: Segue melhores práticas para aplicações web/Laravel.

## 2. Lista de Variáveis Necessárias

| Variável | Descrição | Exemplo Valor | Obrigatório? |
|----------|-----------|---------------|---------------|
| `APP_NAME` | Nome do aplicativo | MeuAppWhatsApp | Sim |
| `APP_ENV` | Ambiente (local, production) | local | Sim |
| `APP_KEY` | Chave de criptografia Laravel (gera com `php artisan key:generate`) | base64:abc123... | Sim |
| `APP_DEBUG` | Habilitar debug (true/false) | false | Sim |
| `APP_URL` | URL base da aplicação | https://meuapp.com | Sim |
| `DB_CONNECTION` | Tipo de banco (mysql, pgsql) | mysql | Sim |
| `DB_HOST` | Host do banco | localhost | Sim |
| `DB_PORT` | Porta do banco | 3306 | Sim |
| `DB_DATABASE` | Nome do banco | meu_app_db | Sim |
| `DB_USERNAME` | Usuário do banco | user | Sim |
| `DB_PASSWORD` | Senha do banco | senha123 | Sim |
| `META_APP_ID` | ID do app Meta (gerado em developers.facebook.com) | 123456789 | Sim |
| `META_APP_SECRET` | App Secret Meta (chave privada) | abcdef123456 | Sim |
| `META_VERIFY_TOKEN` | Token personalizado para verificação de webhooks (crie um único, ex: "meu_token_unico") | meu_token_secreto_2023 | Sim |
| `META_VERSION` | Versão da API Meta | v17.0 | Sim |
| `META_WEBHOOK_URL` | URL do webhook (ex: https://meuapp.com/webhooks/meta ou ngrok) | https://abc123.ngrok.io/webhooks/meta | Sim (para desenvolvimento) |
| `LOG_CHANNEL` | Canal de log (stack, single) | stack | Não |
| `CACHE_DRIVER` | Driver de cache | file | Não |
| `QUEUE_CONNECTION` | Conexão de fila | sync | Não |

### 2.1 Detalhes Específicos das Variáveis Meta

- **META_APP_ID**: Identificador único do seu app no Meta Developers. Usado para autenticação em requests Graph API.
- **META_APP_SECRET**: Chave secreta para gerar access tokens e validar webhooks (nunca expor publicamente).
- **META_VERIFY_TOKEN**: Defina um valor único (alphanumérico, sem espaços). Usado no fluxo de verificação GET dos webhooks (veja `docs/meta-webhooks-setup.md`).
- **META_VERSION**: Versão da API Graph (atual: v17.0). Atualize para a mais recente conforme docs Meta.
- **META_WEBHOOK_URL**: Endpoint público para receber eventos. Em desenvolvimento, use URL ngrok; em produção, domínio fixo (https obrigatório).

## 3. Como Configurar

### Passo 1: Copiar Modelo
```bash
cp .env.example .env
```

### Passo 2: Preencher Valores
Abra `.env` e substitua placeholders pelos valores reais:
- Para META_APP_ID e META_APP_SECRET: Acesse Meta Developers > Apps > Seu App > Settings.
- META_VERIFY_TOKEN: Crie qualquer string secreta (ex: combinação aleatória).
- Variáveis DB: Configure conforme seu servidor de banco.

### Passo 3: Gerar Chave APP_KEY (se Laravel)
```bash
php artisan key:generate
```

### Passo 4: Testar
Execute `php artisan config:cache` e verifique se não há erros.

## 4. Segurança e Boas Práticas

- **Nunca Commite .env**: Adicione a .gitignore para evitar exposição.
- **Valores Sensíveis**: META_APP_SECRET deve ser tratada como senha; use vaults como AWS Secrets Manager em produção.
- **Rotação**: Mude tokens periodicamente (ex: META_VERIFY_TOKEN a cada deploy).
- **Validação**: O app deve falhar graciosamente se variáveis faltarem (use defaults seguros).

## 5. Arquivo .env.example Gerado

Um modelo pronto foi criado em `.env.example` com placeholders. Consulte abaixo ou leia o arquivo diretamente.

Para suporte: [suporte@app.com]
