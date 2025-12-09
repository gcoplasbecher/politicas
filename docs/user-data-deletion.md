# Política e Instruções de Exclusão de Dados do Usuário

## Última Atualização: [Inserir Data Atual]

Este documento detalha como você pode solicitar a exclusão permanente de seus dados pessoais coletados e processados pelo nosso aplicativo. Este processo está em conformidade com a Lei Geral de Proteção de Dados (LGPD) do Brasil, garantindo transparência e controle sobre seus dados.

O direito à exclusão é fundamental e gratuito. Após exclusão, seus dados não potranno ser recuperados.

## 1. Fundamentos Legais

- **Base Legal**: Artigo 18, §1º da LGPD, que garante o direito à anonimização, bloqueio ou eliminação de dados desnecessários, excessivos ou tratados em desconformidade.
- **Escopo**: Aplicável a todos os dados pessoais, incluindo aqueles integrados com APIs da Meta (WhatsApp, Instagram, Graph API).
- **Exceções**: Dados não serão excluídos se houver obrigação legal de retenção (ex: cumprimento de lei, ordens judiciais) ou se necessários para defesa de direitos em processo judicial.

## 2. Dados Elegíveis para Exclusão

- **Dados Coletados**: Nome, e-mail, telefone, Tokens de autenticação (ex: verify_token, app_secret, IDs Meta), logs de uso, histórico de mensagens (armazenado temporariamente).
- **Dados Integrados**: Identificadores enviados via webhooks, conteúdo de mensagens processado pelas APIs Meta.
- **Dados Automáticos**: Dados técnicos como IP, dispositivo, histórico de sessões.

A exclusão inclui anonimização ou eliminação irreversível, conforme o caso.

## 3. Processo de Solicitação de Exclusão

Para solicitar exclusão, siga estes passos:

### Passo 1: Preparação
- Certifique-se de desconectar todas as contas Meta vinculadas no aplicativo (WhatsApp, Instagram).
- Baixe qualquer dado pessoal que desejar reter (ex: histórico de conversas), utilizando o direito de portabilidade na LGPD.

### Passo 2: Submissão da Solicitação
- Envie e-mail para [exclusao@app.com] com o assunto "Solicitação de Exclusão de Dados".
- Inclua as seguintes informações:
  - Nome completo.
  - E-mail associado à conta.
  - Número de telefone (se aplicável).
  - Identificação do usuário (ID da conta ou hash único).
  - Motivo opcional da solicitação (ex:欲 revogação de consentimento).
  - Assinatura digital ou confirmação de identidade (ex: foto de documento).
- Exemplo de Solicitação:
  ```
  Assunto: Solicitação de Exclusão de Dados

  Prezado Suporte,

  Solicito a exclusão permanente de meus dados conforme LGPD. Minhas informações:
  - Nome: [Seu Nome]
  - E-mail: [seuemail@example.com]
  - Motivo: [Descrever]
  - Confirmo ser o titular dos dados.

  Atenciosamente,
  [Seu Nome]
  ```

### Passo 3: Verificação
- Aguarde confirmação de recebimento em até 24 horas.
- Podemos solicitar informações adicionais para verificar identidade, conforme LGPD (art. 19).

### Passo 4: Execução
- Processamento: Até 15 dias após verificação, conforme LGPD.
- Confirmação: E-mail notificando a conclusão da exclusão.
- Em casos complexos (ex: dados distribuídos), prazo estendido para 30 dias.

## 4. Como os Dados São Removidos

- **Armazenamento**: Dados em bancos seguros (ex: criptografados com AES-256) são primeiro anonimizados (removendo identificadores) e depois deletados permanentemente.
- **Servidores e Backup**: Dados excluídos de produção, mídia removida, e cópias de backup eliminadas ou anonimizadas dentro de 30 dias.
- **Integrações Meta**: Tokens e IDs desvinculados das APIs; notificamos Meta se necessário.
- **Logs e Auditoria**: Mantemos registro mínimo da exclusão (sem dados pessoais) para compliance por 5 anos.
- **Irreversibilidade**: Após exclusão, dados não podem ser restaurados; realize backups antes se desejar.

### 4.1 Métodos Técnicos
- Para dados em banco: Comando SQL `DELETE` seguido de `ANONYMIZE`.
- Para arquivos: Remoção física em storage cloud.
- Webhooks: Desconexão do endpoint, exclusão de historial de notificações.

## 5. Direito de Portabilidade Antes da Exclusão

Antes de excluir, você pode solicitar portabilidade:
- Envie solicitação para [portabilidade@app.com].
- Recebido em formato estruturado (JSON/XML) em até 15 dias.

## 6. Outros Direitos Relacionados (LGPD)

- **Acesso**: Veja dados processados enviando para [acesso@app.com].
- **Correção**: Solicite atualizações via [correcao@app.com].
- **Revogação de Consentimento**: Para dados opcionais, disponíve em configurações do app.

Em caso de recusa ou dúvida, pode reclamar à ANPD (Autoridade Nacional de Proteção de Dados).

## 7. Contato

Para solicitações de exclusão ou dúvidas:
- E-mail: [exclusao@app.com]
- Telefone: [inserir telefone]
- DPO (Data Protection Officer): [nome e contato]

---

Esta política garante controle sobre seus dados. Solicitações são processadas com prioridade máxima.
