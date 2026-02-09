# Evolution API - Configuração e Deploy com Docker

Este documento descreve o processo de configuração e deploy do **Evolution API** utilizando Docker Compose, incluindo detalhes de configuração de ambiente, chave de API e sessões do WhatsApp.

## Subida do container

O Evolution API é executado via **Docker Compose**. Antes de iniciar, configure o arquivo `.env` com os parâmetros necessários para a integração com WhatsApp (via Baileys).

Exemplo básico de `.env`:


`INTEGRATION=WHATSAPP`

`PROVIDER=BAILEYS`

`INTEGRATION_ENABLE=true`

`CONFIG_SESSION_PHONE_VERSION=20000000000`

> A variável `CONFIG_SESSION_PHONE_VERSION` é necessária para que o QR Code seja gerado corretamente.


## Chave de ApiKey 

A chave pode ser gerada de forma aleatória sem impactar no processo de implementação. Feito isso, necessário colocar
a chave no `.env`

`AUTHENTICATION_API_KEY= <SUA CHAVE API>`



## Subir o Container 

Para subir os containers:

```bash
docker compose up -d

```

## Volume de sessões

As sessões do WhatsApp são armazenadas em um volume persistente:

```bash
volumes:
  evolution_sessions:/evolution/sessions
  ```


O conteúdo deste volume fica em /evolution/sessions dentro do container.

Para manter a sessão, não é necessário apagar o volume entre reinicializações.

Para gerar um novo QR Code, basta apagar o conteúdo do volume com o container parado:


```bash
docker compose down
docker volume rm evolution_sessions
docker compose up -d
```

