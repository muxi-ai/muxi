---
title: Ruby SDK
description: Native Ruby client for MUXI formations
---
# Ruby SDK

**GitHub:** [`muxi-ai/muxi-ruby`](https://github.com/muxi-ai/muxi-ruby)

## Installation

```bash
gem install muxi
```

## Formation Client

```ruby
require 'json'
require 'muxi'

client = Muxi::FormationClient.new(
  server_url: 'http://localhost:7890',
  formation_id: 'my-assistant',
  client_key: 'fmc_...',
  admin_key: 'fma_...'
)

puts client.health
```

Set `mode: 'draft'` for local drafts, or `base_url:` for a direct Runtime `/v1`
connection.

## Chat

```ruby
response = client.chat(
  { message: 'Hello!', session_id: 'sess_abc123' },
  user_id: 'user_123'
)
puts response['response']
```

## Streaming

```ruby
client.chat_stream({ message: 'Tell me a story' }, user_id: 'user_123') do |event|
  next unless event['event'] == 'message'

  payload = JSON.parse(event['data'])
  print payload['token'] if payload['token'].is_a?(String)
end
```

`Muxi.parse_ui_widgets(event)` extracts widgets from a named `ui` event. Stream
error frames are raised as SDK exceptions.

## Sessions and Memory

```ruby
sessions = client.get_sessions('user_123', limit: 50)
messages = client.get_session_messages('sess_abc123', 'user_123')
memories = client.get_memories('user_123')
client.add_memory('user_123', 'preference', 'User prefers Ruby')
client.clear_user_buffer('user_123')
```

## Server Client

Construct `Muxi::ServerClient` with `url`, `key_id`, and `secret_key`. Deploy
and update methods accept a formation ID and the corresponding Server RPC
payload.

## Learn More

- [SDK Overview](README.md)
- [Streaming Responses](../deep-dives/real-time-streaming.md)
- [API Reference](../reference/api-reference.md)
