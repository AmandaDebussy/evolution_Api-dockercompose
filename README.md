# Evolution API - Configuração e Deploy com Docker e Zabbix Alerta

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



# Criando Alert no Zabbix (WhatsApp - Evolution API)

## Pré-requisito
É necessário que o **Zabbix esteja instalado e funcionando** para realizar a configuração do alerta.

---

## Importando o Tipo de Mídia (WhatsApp - Evolution API)

1. No menu lateral do Zabbix, acesse:

   **Alertas → Tipos de mídia**

2. No canto superior direito da tela, clique em:

   **Importar**

3. Selecione o arquivo:

    `zabbix-mediatype-WhatEvolutionApi.yaml`


## Configurando o Tipo de Mídia

Após importar o arquivo:

1. Localize o tipo de mídia com o nome:

2. Clique para editar.

3. No campo **Valor**, insira a **mesma chave de API** que foi configurada no arquivo `.env` da Evolution API.

4. No campo de formatação da mensagem, selecione:



Isso permitirá que mensagens com tópicos importantes apareçam em **negrito** e com melhor formatação no WhatsApp.

5. Salve as alterações.

---

## Criando a Ação de Alerta

1. Acesse:

**Alertas → Ações**

2. Selecione:

**Tipo de evento: Trigger**

3. Clique em **Criar ação**.

4. Configure:

- Nome da ação
- Condições (ex: Severidade, Host, Grupo, etc.)
- Em **Operações**, selecione o tipo de mídia:
  
  ```
  WhatsApp - Evolution
  ```

5. Salve a ação.

---

## Associando a Mídia ao Usuário

1. Vá em:

**Usuários → Administrador (ou usuário desejado)**

2. Acesse a aba:

**Mídia**

3. Clique em **Adicionar**

4. No campo de destino, informe o número no formato:




5. Salve e depois clique em **Atualizar**.

---

## Testando o Alerta

1. Gere um problema manualmente (ou simule um trigger).
2. Aguarde o disparo da ação.
3. Verifique se a mensagem foi recebida no WhatsApp.

Se configurado corretamente, o alerta será enviado automaticamente via Evolution API.



