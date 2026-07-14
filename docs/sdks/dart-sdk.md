---
title: Dart SDK
description: Native Dart client for MUXI formations
---
# Dart SDK

**GitHub:** [`muxi-ai/muxi-dart`](https://github.com/muxi-ai/muxi-dart)

## Installation

```bash
dart pub add muxi
```

## Formation Client

```dart
import 'dart:convert';
import 'dart:io';
import 'package:muxi/muxi.dart';

final client = FormationClient(FormationConfig(
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: 'fmc_...',
  adminKey: 'fma_...',
));

print(await client.health());
```

Set `mode: 'draft'` for local drafts, or `baseUrl` for a direct Runtime `/v1`
connection.

## Chat

```dart
final response = await client.chat(
  {'message': 'Hello!', 'session_id': 'sess_abc123'},
  userId: 'user_123',
);
print(response?['response']);
```

## Streaming

```dart
await for (final event in client.chatStream(
  {'message': 'Tell me a story'},
  userId: 'user_123',
)) {
  if (event.event != 'message') continue;
  final payload = jsonDecode(event.data) as Map<String, dynamic>;
  if (payload['token'] is String) stdout.write(payload['token']);
}
```

`parseUiWidgets(event)` extracts widgets from a named `ui` event. Stream errors
are raised as SDK exceptions.

## Sessions and Memory

```dart
final sessions = await client.getSessions('user_123', limit: 50);
final messages = await client.getSessionMessages('sess_abc123', 'user_123');
final memories = await client.getMemories('user_123');
await client.addMemory('user_123', 'preference', 'User prefers Dart');
await client.clearUserBuffer('user_123');
```

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
