# Configuração de Webhooks para Meta APIs (WhatsApp / Instagram)

## Última Atualização: 09/12/2025

Este guia técnico detalha o passo a passo para configurar e testar webhooks para integrar com as APIs da Meta Platforms (WhatsApp Business API, Instagram Graph API e Graph API geral). Webhooks permitem receber notificações em tempo real sobre eventos como mensagens recebidas ou interações.

A configuração envolve tokens seguros, endpoints públicos e validação. Suporta desenvolvimento local (via ngrok) e produção (Vercel, AWS, etc.).

## 1. Pré-Requisitos

- **Conta de Desenvolvedor Meta**: Acesso ao [Meta Developers](https://developers.facebook.com/). Crie um app para WhatsApp ou Instagram.
- **App ID e App Secret**: Gerados no painel do appMeta.
- **Variables de Ambiente**: Configurate conforme `docs/env-variables.md`:
  - `META_APP_ID`
  - `META_APP_SECRET`
  - `META_VERIFY_TOKEN` (crie um token único, ex: "meu_token_secreto").
- **Endpoint de Webhook**: URL pública acessível pela Meta (sem https local diretamente).
- **Ferramentas**:
  - Laravel / Node.js para servidor.
  - ngrok para tunneling local.
  - Vercel para deploy rápido.

## 2. Como os Webhooks Funcionam

### 2.1 Fluxo de Verificação (GET Request)
- **Purpose**: Meta envia um GET para verificar se o endpoint é válido.
- **Parâmetros Esperados**:
  - `hub.mode`: Deve ser "subscribe".
  - `hub.challenge`: Valor aleatório a ser retornado.
  - `hub.verify_token`: Comparado com seu `META_VERIFY_TOKEN`.
- **Resposta**: Retorne `hub.challenge` se tokens baterem.

### 2.2 Fluxo de Notificações (POST Request)
- **Eventos**: Envio de mensagens, status updates, interações.
- **Corpo**: JSON com dados do evento (ex: mensagem recebida via WhatsApp).
- **Validação**: Verifique o hash SHA-256 no header `X-Hub-Signature` usando `META_APP_SECRET`.

## 3. Passo a Passo de Configuração

### Passo 1: Criar Endpoint no Servidor
Implemente rotas para webhooks. Exemplo em Laravel (PHP):

```php
// routes/web.php
Route::get('/webhooks/meta', [WebhookController::class, 'verify']);
Route::post('/webhooks/meta', [WebhookController::class, 'handle']);
```

```php
// app/Http/Controllers/WebhookController.php
class WebhookController extends Controller
{
    public function verify(Request $request)
    {
        $mode = $request->input('hub.mode');
        $token = $request->input('hub.verify_token');
        $challenge = $request->input('hub.challenge');

        if ($mode === 'subscribe' && $token === config('app.meta_verify_token')) {
            return response($challenge, 200);
        }

        return response('Forbidden', 403);
    }

    public function handle(Request $request)
    {
        $signature = $request->header('X-Hub-Signature-256');
        $body = $request->getContent();
        $expected = 'sha256=' . hash_hmac('sha256', $body, config('app.meta_app_secret'));

        if (!hash_equals($signature, $expected)) {
            return response('Unauthorized', 403);
        }

        // Processar evento (ex: salvar mensagem)
        Log::info('Webhook received: ' . $body);
        return response('Ok', 200);
    }
}
```

Para Node.js:

```javascript
// server.js
const express = require('express');
const crypto = require('crypto');
const app = express();

app.get('/webhooks/meta', (req, res) => {
    const mode = req.query['hub.mode'];
    const token = req.query['hub.verify_token'];
    const challenge = req.query['hub.challenge'];

    if (mode === 'subscribe' && token === process.env.META_VERIFY_TOKEN) {
        res.status(200).send(challenge);
    } else {
        res.status(403).send('Forbidden');
    }
});

app.post('/webhooks/meta', (req, res) => {
    const signature = req.headers['x-hub-signature-256'];
    const body = JSON.stringify(req.body);
    const expected = 'sha256=' . crypto.createHmac('sha256', process.env.META_APP_SECRET).update(body, 'utf8').digest('hex');

    if (signature !== `sha256=${expected}`) {
        return res.status(403).send('Unauthorized');
    }

    // Processar evento
    console.log('Evento:', req.body);
    res.status(200).send('Ok');
});

// Para testar localmente
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

### Passo 2: Expor o Endpoint Localmente (ngrok)

1. Instale ngrok: `npm install -g ngrok` ou baixe do site.
2. Execute seu servidor local (ex: `php artisan serve` ou `node server.js`).
3. Tunel: `ngrok http 8000` (porta do Laravel padrão).
4. Copie a URL gerada (ex: `https://abc123.ngrok.io`). Adicione `/webhooks/meta`.
5. Defina .env:
   ```
   META_WEBHOOK_URL=https://abc123.ngrok.io/webhooks/meta
   ```

### Passo 3: Configurar no Meta Developers

1. Acesse seu app no Meta Developers > Webhooks.
2. Adicione tópicos:
   - Para WhatsApp: `messages`, `message_deliveries`.
   - Para Instagram: `messages`, `messaging_postbacks`.
3. Insira:
   - Callback URL: Sua URL ngrok + `/webhooks/meta`.
   - Verify Token: Seu `META_VERIFY_TOKEN`.
   - App Secret: Seu `META_APP_SECRET`.
4. Clique em "Verify and Save" – Meta fará um GET para validar.

### Passo 4: Teste de Verificação
- Use um tool como Postman para simular GET:
  - URL: `https://abc123.ngrok.io/webhooks/meta?hub.mode=subscribe&hub.challenge=test_challenge&hub.verify_token=meu_token_secreto`
  - Esperado: Status 200 com "test_challenge".

### Passo 5: Deploy para Produção (Vercel)

1. Organize código em projeto Vercel (api/webhooks/meta.js).
2. Deploy: `vercel --prod`.
3. Atualize Callback URL no Meta para a URL Vercel (ex: `https://app-name.vercel.app/api/webhooks/meta`).
4. Teste com mensagens reais.

## 4. Solução de Problemas

- **Erro 403 no GET**: Verifique `META_VERIFY_TOKEN` no .env e painel Meta.
- **Erro no POST**: Valide hash SHA-256; certifique-se de JSON.parse no corpo.
- **URL Inacessível**: Use https; ngrok grátis expira a cada 8 horas – monte novo.
- **Limites**: Meta limita eventos por app; configure rate limits no servidor.
- **Logs**: Habilitar logs detalhados para debug.

| Problema | Causa Comum | Solução |
|----------|-------------|---------|
| Challenge não retornado | Token errado | Verifique .env |
| Hash não batendo | Corpo JSON malformado | Use JSON.stringify |
| Webhook não chamado | URL errada | Confirme https e rota |

## 5. Segurança Adicional

- Use HTTPS sempre.
- Valide todas as requests.
- Limite IPs se possível (Meta IPs conhecidos).
- Rotacione tokens periodicamente.

## 6. Referências

- [Meta Webhooks Docs](https://developers.facebook.com/docs/graph-api/webhooks/)
- [WhatsApp Business API](https://developers.facebook.com/docs/whatsapp/)
- [Instagram Graph API](https://developers.facebook.com/docs/instagram-api/)

Para suporte: [suporte@app.com]
