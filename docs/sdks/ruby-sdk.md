---
title: Ruby SDK
description: Native Ruby client for MUXI formations
---
# Ruby SDK

## Idiomatic Ruby access to your agents

Build Ruby applications that interact with MUXI formations. Full support for chat, streaming, sessions, and all Formation API operations.

**GitHub:** [`muxi-ai/muxi-ruby`](https://github.com/muxi-ai/muxi-ruby)  
**RubyGems:** [`muxi`](https://rubygems.org/gems/muxi)

## Installation

```bash
gem install muxi
```

Or add to your Gemfile:

```ruby
gem 'muxi'
```

## Quick Start

```ruby
require 'muxi'

client = Muxi::FormationClient.new(
  server_url: 'http://localhost:7890',
  formation_id: 'my-assistant',
  client_key: 'your_client_key'
)

# Check health
puts client.health
```

## Chat (Streaming)

```ruby
client.chat_stream(message: 'Hello!', user_id: 'user_123') do |event|
  case event['type']
  when 'text'
    print event['text']
  when 'done'
    break
  end
end
```

## Chat (Non-Streaming)

```ruby
response = client.chat(message: 'Hello!', user_id: 'user_123')
puts response['response']
```

## Memory

```ruby
# Get memories
memories = client.get_memories(user_id: 'user_123')

# Add memory
client.add_memory(
  user_id: 'user_123',
  mem_type: 'preference',
  detail: 'User prefers Ruby'
)

# Clear buffer
client.clear_user_buffer(user_id: 'user_123')
```

## Sessions

```ruby
# List sessions
sessions = client.get_sessions(user_id: 'user_123')

# Get session messages
messages = client.get_session_messages(
  session_id: 'sess_abc123',
  user_id: 'user_123'
)
```

## Server Client

For managing formations (deploy, start, stop):

```ruby
server = Muxi::ServerClient.new(
  url: 'http://localhost:7890',
  key_id: 'muxi_pk_...',
  secret_key: 'muxi_sk_...'
)

# List formations
formations = server.list_formations

# Deploy
server.deploy_formation(bundle_path: 'my-bot.tar.gz')

# Stop/start/restart
server.stop_formation(formation_id: 'my-bot')
server.start_formation(formation_id: 'my-bot')
```

## Error Handling

```ruby
begin
  response = client.chat(message: 'Hello!', user_id: 'user_123')
rescue Muxi::AuthenticationError => e
  puts "Auth failed: #{e.message}"
rescue Muxi::RateLimitError => e
  puts "Rate limited, retry after: #{e.retry_after}s"
rescue Muxi::MuxiError => e
  puts "Error: #{e.code} - #{e.message}"
end
```

## Learn More

- [Full documentation on GitHub](https://github.com/muxi-ai/muxi-ruby)
- [API Reference](../reference/api-reference.md)
