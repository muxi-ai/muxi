---
title: PHP SDK
description: Native PHP client for MUXI formations
---
# PHP SDK

**GitHub:** [`muxi-ai/muxi-php`](https://github.com/muxi-ai/muxi-php)  
**Packagist:** [`muxi/muxi-php`](https://packagist.org/packages/muxi/muxi-php)

## Installation

```bash
composer require muxi/muxi-php
```

## Formation Client

```php
<?php
use Muxi\FormationClient;

$client = new FormationClient([
    'serverUrl' => 'http://localhost:7890',
    'formationId' => 'my-assistant',
    'clientKey' => 'fmc_...',
    'adminKey' => 'fma_...',
]);

print_r($client->health());
```

Set `'mode' => 'draft'` for local draft routes. Set `baseUrl` to a Runtime's
`/v1` URL for a direct connection.

## Chat

```php
$response = $client->chat([
    'message' => 'Hello!',
    'session_id' => 'sess_abc123',
], 'user_123');

echo $response['response'];
```

## Streaming

The SDK passes raw SSE events to a callback:

```php
$client->chatStream(
    ['message' => 'Tell me a story'],
    'user_123',
    function (array $event): void {
        if ($event['event'] !== 'message') {
            return;
        }
        $payload = json_decode($event['data'], true, 512, JSON_THROW_ON_ERROR);
        if (is_string($payload['token'] ?? null)) {
            echo $payload['token'];
        }
    }
);
```

`FormationClient::parseUiWidgets($event)` extracts widgets from a named `ui`
event. Stream error frames are raised as SDK exceptions.

## Sessions and Memory

```php
$sessions = $client->getSessions('user_123', 50);
$messages = $client->getSessionMessages('sess_abc123', 'user_123');
$memories = $client->getMemories('user_123');
$client->addMemory('user_123', 'preference', 'User prefers PHP');
$client->clearUserBuffer('user_123');
```

## Server Client

```php
use Muxi\ServerClient;

$server = new ServerClient([
    'url' => 'http://localhost:7890',
    'keyId' => 'muxi_pk_...',
    'secretKey' => 'muxi_sk_...',
]);

$formations = $server->listFormations();
$server->stopFormation('my-assistant');
$server->startFormation('my-assistant');
$server->rollbackFormation('my-assistant');
```

Deploy and update methods accept a formation ID and the corresponding Server
RPC payload array.

## Error Handling

```php
use Muxi\AuthenticationException;
use Muxi\MuxiException;

try {
    $client->chat(['message' => 'Hello!'], 'user_123');
} catch (AuthenticationException $error) {
    echo $error->getMessage();
} catch (MuxiException $error) {
    echo $error->errorCode . ': ' . $error->getMessage();
}
```

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
