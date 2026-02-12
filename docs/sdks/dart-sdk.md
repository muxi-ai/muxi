---
title: Dart SDK
description: Native Dart client for MUXI formations
---
# Dart SDK

## Dart access to your agents

Build Dart and Flutter applications that interact with MUXI formations. Full support for chat, streaming, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-dart`](https://github.com/muxi-ai/muxi-dart)  
**pub.dev:** [`muxi`](https://pub.dev/packages/muxi)

## Installation

```bash
dart pub add muxi
```

Or add to your `pubspec.yaml`:

```yaml
dependencies:
  muxi: ^0.1.0
```

## Quick Start

```dart
import 'package:muxi/muxi.dart';

final client = FormationClient(
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: 'your_client_key',
);

// Check health
final health = await client.health();
print(health);
```

## Chat (Streaming)

```dart
await for (final event in client.chatStream(
  message: 'Hello!',
  userId: 'user_123',
)) {
  if (event['type'] == 'text') {
    stdout.write(event['text']);
  } else if (event['type'] == 'done') {
    break;
  }
}
```

## Chat (Non-Streaming)

```dart
final response = await client.chat(
  message: 'Hello!',
  userId: 'user_123',
);
print(response['response']);
```

## Memory

```dart
// Get memories
final memories = await client.getMemories('user_123');

// Add memory
await client.addMemory('user_123', 'preference', 'User prefers Dart');

// Clear buffer
await client.clearUserBuffer('user_123');
```

## Sessions

```dart
// List sessions
final sessions = await client.getSessions('user_123');

// Get session messages
final messages = await client.getSessionMessages('sess_abc123', 'user_123');
```

## Server Client

For managing formations (deploy, start, stop):

```dart
import 'package:muxi/muxi.dart';

final server = ServerClient(
  url: 'http://localhost:7890',
  keyId: 'muxi_pk_...',
  secretKey: 'muxi_sk_...',
);

// List formations
final formations = await server.listFormations();

// Deploy
await server.deployFormation('my-bot.tar.gz');

// Stop/start/restart
await server.stopFormation('my-bot');
await server.startFormation('my-bot');
```

## Error Handling

```dart
import 'package:muxi/muxi.dart';

try {
  final response = await client.chat(
    message: 'Hello!',
    userId: 'user_123',
  );
} on AuthenticationException catch (e) {
  print('Auth failed: ${e.message}');
} on RateLimitException catch (e) {
  print('Rate limited, retry after: ${e.retryAfter}s');
} on MuxiException catch (e) {
  print('Error: ${e.code} - ${e.message}');
}
```

## Flutter Integration

```dart
import 'package:flutter/material.dart';
import 'package:muxi/muxi.dart';

class ChatWidget extends StatefulWidget {
  @override
  _ChatWidgetState createState() => _ChatWidgetState();
}

class _ChatWidgetState extends State<ChatWidget> {
  final client = FormationClient(
    serverUrl: 'http://localhost:7890',
    formationId: 'my-assistant',
    clientKey: 'your_client_key',
  );
  
  String response = '';

  Future<void> sendMessage(String message) async {
    await for (final event in client.chatStream(
      message: message,
      userId: 'user_123',
    )) {
      if (event['type'] == 'text') {
        setState(() {
          response += event['text'];
        });
      }
    }
  }

  // ... build method
}
```

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-dart)
- [API Reference](../reference/api-reference.md)
