---
title: PHP SDK
description: Native PHP client for MUXI formations
---
# PHP SDK

## PHP access to your agents

Build PHP applications that interact with MUXI formations. Full support for chat, streaming, sessions, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-php`](https://github.com/muxi-ai/muxi-php)  
**Packagist:** [`muxi/muxi-php`](https://packagist.org/packages/muxi/muxi-php)

## Installation

```bash
composer require muxi/muxi-php
```

## Quick Start

```php
<?php
use Muxi\FormationClient;

$client = new FormationClient(
    serverUrl: 'http://localhost:7890',
    formationId: 'my-assistant',
    clientKey: 'your_client_key'
);

// Check health
print_r($client->health());
```

## Chat (Streaming)

```php
foreach ($client->chatStream(['message' => 'Hello!'], 'user_123') as $event) {
    if ($event['type'] === 'text') {
        echo $event['text'];
    } elseif ($event['type'] === 'done') {
        break;
    }
}
```

## Chat (Non-Streaming)

```php
$response = $client->chat(['message' => 'Hello!'], 'user_123');
echo $response['response'];
```

## Memory

```php
// Get memories
$memories = $client->getMemories('user_123');

// Add memory
$client->addMemory('user_123', 'preference', 'User prefers PHP');

// Clear buffer
$client->clearUserBuffer('user_123');
```

## Sessions

```php
// List sessions
$sessions = $client->getSessions('user_123');

// Get session messages
$messages = $client->getSessionMessages('sess_abc123', 'user_123');
```

## Server Client

For managing formations (deploy, start, stop):

```php
use Muxi\ServerClient;

$server = new ServerClient(
    url: 'http://localhost:7890',
    keyId: 'muxi_pk_...',
    secretKey: 'muxi_sk_...'
);

// List formations
$formations = $server->listFormations();

// Deploy
$server->deployFormation('my-bot.tar.gz');

// Stop/start/restart
$server->stopFormation('my-bot');
$server->startFormation('my-bot');
```

## Error Handling

```php
use Muxi\Exceptions\{MuxiException, AuthenticationException, RateLimitException};

try {
    $response = $client->chat(['message' => 'Hello!'], 'user_123');
} catch (AuthenticationException $e) {
    echo "Auth failed: " . $e->getMessage();
} catch (RateLimitException $e) {
    echo "Rate limited, retry after: " . $e->getRetryAfter() . "s";
} catch (MuxiException $e) {
    echo "Error: " . $e->getCode() . " - " . $e->getMessage();
}
```

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-php)
- [API Reference](../reference/api-reference.md)
