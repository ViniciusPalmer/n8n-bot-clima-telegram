# Chatbot de Clima no Telegram com N8N

## Descrição do projeto

Este projeto implementa um chatbot no Telegram usando **n8n** para informar a **temperatura atual de qualquer cidade do Brasil**.

O bot recebe uma mensagem no formato:

`Cidade,UF,BR`

Exemplo:

`São Paulo,SP,BR`

Em seguida, o workflow:
1. captura e normaliza a entrada;
2. consulta a API do OpenWeather;
3. valida a resposta;
4. envia ao usuário uma mensagem curta, clara e amigável no Telegram.

Exemplo de retorno:

`🌤️ A temperatura em São Paulo é de 25°C.`

Em caso de erro, o bot responde:

`❌ Cidade não encontrada. Use o formato Cidade,UF,BR (ex.: São Paulo,SP,BR).`

---

## Requisitos atendidos neste workflow

- **Telegram Trigger** como início do fluxo
- captura da mensagem e armazenamento em variável **`queue`**
- normalização da entrada
- chamada à API do OpenWeather via **HTTP Request**
- uso da variável de ambiente **`OPENWEATHER_API_KEY`**
- validação da resposta com **IF node**
- tratamento de erro
- envio da mensagem final com **Telegram Send Message**
- workflow exportado sem tokens ou segredos embutidos

---

## Pré-requisitos

Antes de usar o workflow, você precisa ter:

- uma instância do **n8n**
- um bot criado no **Telegram**
- uma chave da API do **OpenWeather**

Variáveis esperadas:

- `OPENWEATHER_API_KEY`
- `TELEGRAM_BOT_TOKEN`

> Observação: o workflow exportado não contém tokens reais embutidos.

---

## Como importar o workflow no N8N

1. Abra o **n8n**.
2. Clique em **Import from File**.
3. Selecione o arquivo `workflow-chatbot-telegram.json`.
4. Após importar, abra o workflow.

---

## Como configurar as credenciais

### 1) Credencial do Telegram

1. No n8n, abra o nó **Telegram Trigger**.
2. Clique para criar ou selecionar uma credencial do tipo **Telegram API**.
3. Informe o token do bot do Telegram.
4. Salve a credencial.

Valor esperado:
- `TELEGRAM_BOT_TOKEN`

### 2) Variável de ambiente do OpenWeather

Configure no ambiente do n8n a variável abaixo:

```bash
OPENWEATHER_API_KEY=sua_chave_aqui
```

Essa variável é usada no nó **Consultar OpenWeather** no campo `appid`.

---

## Como executar o chatbot

1. Ative o workflow no n8n.
2. Abra a conversa com o seu bot no Telegram.
3. Envie uma cidade no formato:

`Cidade,UF,BR`

Exemplos para teste:

- `São Paulo,SP,BR`
- `Belo Horizonte,MG,BR`
- `Curitiba,PR,BR`

Resposta esperada:

`🌤️ A temperatura em <Cidade> é de <Temperatura>°C.`

---

## Como testar o tratamento de erro

Teste com uma cidade inexistente ou com entrada fora do formato esperado.

Exemplos:
- `CidadeInexistente,ZZ,BR`
- `São Paulo`

Resposta esperada:

`❌ Cidade não encontrada. Use o formato Cidade,UF,BR (ex.: São Paulo,SP,BR).`

---

## Estrutura resumida do workflow

1. **Telegram Trigger**  
   Recebe mensagens de texto enviadas ao bot.

2. **Preparar Entrada**  
   Cria a variável `queue`, remove acentos, ajusta espaços e formata a entrada para o padrão `cidade,UF,BR`.

3. **Entrada válida?**  
   Verifica se a mensagem está no formato esperado.

4. **Consultar OpenWeather**  
   Faz a chamada ao endpoint de clima atual da OpenWeather usando:
   - `q = queue`
   - `units = metric`
   - `lang = pt_br`
   - `appid = $env.OPENWEATHER_API_KEY`

5. **Resposta válida?**  
   Confirma se o status HTTP foi `200` e se os campos esperados existem.

6. **Formatar Resposta**  
   Arredonda a temperatura e monta a mensagem final.

7. **Enviar Temperatura / Enviar Erro**  
   Responde ao usuário no Telegram.
