# Comms Mundi - API v1

## Sumário
- [Sobre](#sobre)
- [API](#api)
  - [Protocol](#protocol)
  - [Autenticação](#autenticação)
  - [Segurança](#segurança)
  - [Parâmetros de objeto na requisição](#parâmetros-de-objeto-na-requisição)
- [Métodos](#métodos)
  - [Diretórios](#diretórios)
  - [Contatos](#contatos)
  - [Callcenter](#callcenter)
  - [Dialplan](#dialplan)
  - [Telefônica](#telefônica)
  - [DB, SIP, Verto e Outros](#db-sip-verto-e-outros)
- [Exemplos de Uso](#exemplos-de-uso)
  - [Exemplo 1: Função genérica `api_call`](#exemplo-1-função-genérica-apicall)
  - [Exemplo 2: Método `help`](#exemplo-2-método-help)
  - [Exemplo 3: Método `tel_monitor_call_get`](#exemplo-3-método-tel_monitor_call_get)
  - [Exemplo 4: Método `tel_util_call`](#exemplo-4-método-tel_util_call)
  - [Exemplo 5: Outros exemplos](#exemplo-5-outros-exemplos)
- [Anexo I – Exemplo de Array de Filtro](#anexo-i--exemplo-de-array-de-filtro)

---

## Sobre

Este manual explica o uso do módulo API da CommsMundi (CM). Nele são listados todos os métodos disponíveis, acompanhados de exemplos básicos de uso em PHP. Outros idiomas podem ser utilizados, bastando adaptar os exemplos conforme necessário.

---

## API

### Protocol

A comunicação com a API pública da CM é feita via **JSON RPC 2.0**.  
Consulte [JSON RPC 2.0 Specification](http://www.jsonrpc.org/specification) para detalhes.  
Principais objetos:
- **Request**: Contém `jsonrpc`, `method`, `params` (parâmetros do método) e `id`.
- **Response**: Contém `jsonrpc`, `result` (em caso de sucesso) ou `error` (quando ocorre erro) e `id`.

### Autenticação

A autenticação é feita via **HTTP BASIC AUTHENTICATION**.  
Exemplo de URL: http://username:password@server_address:server_port

### Segurança

É recomendado utilizar HTTPS para garantir a segurança da conexão com a API.

### Parâmetros de objeto na requisição

Alguns métodos permitem configurar parâmetros dinâmicos na requisição. Os tipos mais comuns são:
- **boolean**: `true` ou `false`
- **string**: valores de texto
- **numeric**: valores numéricos
- **orderby**: para ordenar resultados (ex.: `orderby['name'] = "asc"`)
- **limit**: para limitar o número de resultados (ex.: `"0,10"`)
- **data**: array dinâmico de parâmetros específicos para cada método

---

## Métodos

O manual lista diversos métodos disponíveis na API. Abaixo, uma visão geral agrupada por categorias:

### Diretórios

Métodos para manipulação de diretórios, como:
- `dir_get`, `dir_get_single`, `dir_count`, `dir_add`, `dir_mod`, `dir_del`
- `dir_map_get`, `dir_map_add`, `dir_map_mod`, `dir_map_del`
- Métodos específicos para domínios (`dir_domain_*`), contas (`dir_account_*`) e grupos (`dir_group_*`)
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
- Outros métodos para gravações, SMS, voicemail, etc.

### DB, SIP, Verto e Outros

Métodos para:
- Manipulação de banco de dados (`tel_db_get`, `tel_db_list`, `tel_db_insert`, `tel_db_del`)
- Gerenciamento de templates SIP e verto
- Upload e download de sons, manipulação de IVRs e ações relacionadas

---

## Exemplos de Uso

O manual contém diversos exemplos de código em PHP. Alguns deles são:

### Exemplo 1: Função genérica `api_call`

Exemplo para realizar uma chamada à API utilizando a função `api_call`:
```php
<?php include("api_call.inc.php");
$server = "192.168.8.2:444";
$url = "http://administrator:commsmundi@".$server;
$request = array();
$request['id'] = mt_rand();
$request['jsonrpc'] = "2.0";
$request['method'] = "help";
$data = api_call($url, $request);
if (isset($data['result'])) {
  foreach ($data['result'] as $method => $desc) {
    echo $method.",".$desc."\n";
  }
}
?>

### Exemplo 2: Método help
Exemplo para obter ajuda sobre os métodos disponíveis:
<?php include("api_call.inc.php");
$server = "192.168.8.2:444";
$url = "http://administrator:commsmundi@".$server;
$request = array();
$request['id'] = mt_rand();
$request['jsonrpc'] = "2.0";
$request['method'] = "help";
$request['params']['method_name'] = "help";
$data = api_call($url, $request, false);
echo($data);
?>

### Exemplo 3: Método tel_monitor_call_get
Exemplo para listar chamadas ativas:
<?php include("api_call.inc.php");
$server = "192.168.8.2:444";
$url = "http://administrator:commsmundi@".$server;
$request = array();
$request['id'] = mt_rand();
$request['jsonrpc'] = "2.0";
$request['method'] = "tel_monitor_call_get";
$request['params']['limit'] = "0,10";
$data = api_call($url, $request, false);
echo($data);
?>

### Exemplo 4: Método tel_util_call
Exemplo para realizar uma chamada entre dois números:
<?php include("api_call.inc.php");
$server = "192.168.8.2:444";
$url = "http://administrator:commsmundi@".$server;
$request = array();
$request['id'] = mt_rand();
$request['jsonrpc'] = "2.0";
$request['method'] = "tel_util_call";
$request['params'] = array();
$request['params']['aleg_destination'] = "200";
$request['params']['aleg_context'] = "outgoing";
$request['params']['bleg_destination'] = "201";
$request['params']['bleg_context'] = "outgoing";
$data = api_call($url, $request, false);
echo($data);
?>

### Exemplo 5: Outros exemplos
O documento contém também exemplos para métodos de hangup, exportação de campanhas, manipulação de agentes, conferências, entre outros. Basta seguir a estrutura dos exemplos acima e adaptar conforme a necessidade.

### Anexo I – Exemplo de Array de Filtro
Exemplos de como criar arrays para filtros:

### Filtro simples:
php
Copiar
Editar
$filter['name'] = "200";

### Filtro múltiplo:
php
Copiar
Editar
$filter['name'] = "200";
$filter['type'] = "endpoint";

### Filtro complexo com LIKE:
php
Copiar
Editar
$filter[] = array("name" => "name", "operator" => "LIKE", "value" => "%200%");

### Filtro complexo com NOT LIKE:
php
Copiar
Editar
$filter[] = array("name" => "name", "operator" => "NOT LIKE", "value" => "200%");

### Filtro com operador >:
php
Copiar
Editar
$filter[] = array("name" => "duration", "operator" => ">", "value" => "3600");

### Filtro com operador !=:
php
Copiar
Editar
$filter[] = array("name" => "name", "operator" => "!=", "value" => "200");


---
