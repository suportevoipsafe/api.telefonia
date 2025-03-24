# Comms Mundi - API v1

## Sumário
- [Sobre](#sobre)
- [API](#api)
  - [Protocol](#protocol)
  - [Autenticação](#autenticacao)
  - [Segurança](#seguranca)
  - [Parâmetros de objeto na requisição](#parametros-de-objeto-na-requisicao)
- [Métodos](#metodos)
  - [Diretórios](#diretorios)
  - [Contatos](#contatos)
  - [Callcenter](#callcenter)
  - [Dialplan](#dialplan)
  - [Telefônica](#telefonica)
  - [DB, SIP, Verto e Outros](#db-sip-verto-e-outros)
- [Exemplos de Uso](#exemplos-de-uso)
  - [Exemplo 1: Função genérica `api_call`](#exemplo-1-funcao-generica-apicall)
  - [Exemplo 2: Método `help`](#exemplo-2-metodo-help)
  - [Exemplo 3: Método `tel_monitor_call_get`](#exemplo-3-metodo-tel_monitor_call_get)
  - [Exemplo 4: Método `tel_util_call`](#exemplo-4-metodo-tel_util_call)
  - [Exemplo 5: Outros exemplos](#exemplo-5-outros-exemplos)
- [Anexo I – Exemplo de Array de Filtro](#anexo-i-exemplo-de-array-de-filtro)

---

## Sobre

Este manual explica o uso do módulo API da CommsMundi (CM). Nele são listados todos os métodos disponíveis, acompanhados de exemplos básicos de uso em PHP. Outros idiomas podem ser utilizados, bastando adaptar os exemplos conforme necessário.

---

## API

### Protocol

A comunicação com a API pública da CM é feita via **JSON RPC 2.0**.
Consulte [JSON RPC 2.0 Specification](http://www.jsonrpc.org/specification) para detalhes.

### Autenticação

A autenticação é feita via **HTTP BASIC AUTHENTICATION**.
Exemplo de URL: `http://username:password@server_address:server_port`

### Segurança

É recomendado utilizar HTTPS para garantir a segurança da conexão com a API.

### Parâmetros de objeto na requisição

Tipos mais comuns:
- **boolean**: `true` ou `false`
- **string**: valores de texto
- **numeric**: valores numéricos
- **orderby**: ordenar resultados (ex.: `orderby['name'] = "asc"`)
- **limit**: limitar o número de resultados (ex.: `"0,10"`)
- **data**: array dinâmico de parâmetros específicos para cada método

---

## Métodos

### Diretórios

Métodos para manipulação de diretórios:
- `dir_get`, `dir_get_single`, `dir_count`, `dir_add`, `dir_mod`, `dir_del`
- `dir_map_get`, `dir_map_add`, `dir_map_mod`, `dir_map_del`
- Métodos para domínios (`dir_domain_*`), contas (`dir_account_*`) e grupos (`dir_group_*`)
- `dir_apply`: Aplica as alterações realizadas

### Contatos

Métodos para manipulação de contatos:
- `contact_get`, `contact_get_single`, `contact_count`, `contact_add`, `contact_mod`, `contact_del`
- `contact_apply`: Aplica as alterações no sistema

### Callcenter

Métodos relacionados a callcenter:
- `tel_callcenter_get`, `tel_callcenter_get_single`, `tel_callcenter_count`
- `tel_callcenter_add`, `tel_callcenter_mod`, `tel_callcenter_del`, `tel_callcenter_apply`
- Métodos para filas, campanhas, agentes, chamadas e logs

### Dialplan

Métodos para gerenciamento do dialplan:
- `tel_dp_context_get`, `tel_dp_context_add`, `tel_dp_context_mod`, `tel_dp_context_del`
- `tel_dp_extension_*`: Obtenção, adição, modificação e deleção de regras de extensão
- `tel_dp_apply`: Aplica as mudanças no dialplan

### Telefônica

Métodos para chamadas e monitoramento:
- `tel_monitor_endpoint_get`, `tel_monitor_call_get`, `tel_monitor_call_get_single`
- `tel_util_call`: Para realizar chamadas
- `tel_util_hangup`: Para desligar chamadas ativas

### DB, SIP, Verto e Outros

Métodos para:
- Manipulação de banco de dados (`tel_db_get`, `tel_db_list`, `tel_db_insert`, `tel_db_del`)
- Gerenciamento de templates SIP e Verto
- Upload e download de sons, manipulação de IVRs

---

## Exemplos de Uso

### Exemplo 1: Função genérica `api_call`

```php
include("api_call.inc.php");
$server = getenv('API_SERVER');
$url = "http://" . getenv('API_USERNAME') . ":" . getenv('API_PASSWORD') . "@" . $server;
$request = [
  'id' => mt_rand(),
  'jsonrpc' => "2.0",
  'method' => "help"
];
$data = api_call($url, $request);
print_r($data);
```

### Exemplo 2: Método `help`

```php
$request['params']['method_name'] = "help";
$data = api_call($url, $request, false);
echo($data);
```

### Exemplo 3: Método `tel_monitor_call_get`

```php
$request['method'] = "tel_monitor_call_get";
$request['params']['limit'] = "0,10";
$data = api_call($url, $request, false);
echo($data);
```

### Exemplo 4: Método `tel_util_call`

```php
$request['method'] = "tel_util_call";
$request['params'] = [
  'aleg_destination' => "200",
  'aleg_context' => "outgoing",
  'bleg_destination' => "201",
  'bleg_context' => "outgoing"
];
$data = api_call($url, $request, false);
echo($data);
```

### Exemplo 5: Outros exemplos

O documento contém exemplos para métodos de hangup, exportação de campanhas, manipulação de agentes, conferências, entre outros.

---

## Anexo I – Exemplo de Array de Filtro

### Filtro simples:

```php
$filter['name'] = "200";
```

### Filtro múltiplo:

```php
$filter['name'] = "200";
$filter['type'] = "endpoint";
```

### Filtro complexo com LIKE:

```php
$filter[] = ["name" => "name", "operator" => "LIKE", "value" => "%200%"];
```

### Filtro com operador >:

```php
$filter[] = ["name" => "duration", "operator" => ">", "value" => "3600"];
```

### Filtro com operador !=:

```php
$filter[] = ["name" => "name", "operator" => "!=", "value" => "200"];
