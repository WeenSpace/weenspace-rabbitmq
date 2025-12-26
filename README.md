# WeenSpace RabbitMQ Client

[![PyPI version](https://badge.fury.io/py/weenspace-rabbitmq.svg)](https://pypi.org/project/weenspace-rabbitmq/)
[![Python 3.13+](https://img.shields.io/badge/python-3.13+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A powerful Python RabbitMQ client for AMQP 1.0 protocol, designed for event-driven microservices architecture.

## ✨ Features

- 🔐 **Authentication**: OAuth2, TLS/SSL, Basic Auth
- 📬 **Queue Types**: Classic, Quorum, Stream queues
- ☠️ **Dead Letter Queues**: Built-in DLQ support with configurable strategies
- ⚡ **Priority Queues**: Native priority queue support
- 🔄 **Auto Reconnection**: Configurable recovery with exponential backoff
- 🔀 **All Exchange Types**: Direct, Fanout, Topic, Headers
- 🚀 **Async Support**: Native asyncio integration
- 📊 **Streams**: RabbitMQ Streams with filtering support

## 📦 Installation

```bash
pip install weenspace-rabbitmq
```

## 🚀 Quick Start

### Basic Publisher/Consumer

```python
from weenspace_rabbitmq import (
    Environment,
    Connection,
    Publisher,
    Consumer,
    Message,
    ClassicQueueSpecification,
    ExchangeSpecification,
)

# Create environment and connection
environment = Environment(uri="amqp://guest:guest@localhost:5672/")
connection = environment.connection()

# Declare queue with dead letter support
management = connection.management()
queue_spec = ClassicQueueSpecification(
    name="my-queue",
    is_durable=True,
    dead_letter_exchange="dlx",
    dead_letter_routing_key="dlx-key",
    max_priority=10,  # Enable priority
)
management.declare_queue(queue_spec)

# Publish message
publisher = connection.publisher("/queues/my-queue")
publisher.publish(Message(body=b"Hello WeenSpace!"))

# Consume messages
def on_message(message):
    print(f"Received: {message.body}")
    message.accept()

consumer = connection.consumer("/queues/my-queue", handler=on_message)
```

### Async Support

```python
import asyncio
from weenspace_rabbitmq.asyncio import AsyncEnvironment

async def main():
    async with AsyncEnvironment(uri="amqp://localhost:5672/") as env:
        async with env.connection() as conn:
            publisher = await conn.publisher("/queues/my-queue")
            await publisher.publish(Message(body=b"Async message!"))

asyncio.run(main())
```

### OAuth2 Authentication

```python
from weenspace_rabbitmq import Environment, OAuth2Options

oauth = OAuth2Options(token="your_jwt_token")
environment = Environment(
    uri="amqp://localhost:5672/",
    oauth2_options=oauth
)
```

### TLS/SSL Connection

```python
from weenspace_rabbitmq import Environment, SslConfiguration

ssl_config = SslConfiguration(
    ca_cert="/path/to/ca.pem",
    client_cert="/path/to/client.pem",
    client_key="/path/to/client.key"
)
environment = Environment(
    uri="amqps://localhost:5671/",
    ssl_configuration=ssl_config
)
```

## 📚 Documentation

See [examples](./examples) folder for more detailed usage examples.

## 🔄 Migration from python-rabbitmq

```python
# Old (python-rabbitmq)
# from python_rabbitmq import RabbitMQ

# New (weenspace-rabbitmq)
from weenspace_rabbitmq import Environment, Connection
```

## 📋 Requirements

- Python 3.13+
- RabbitMQ 4.x with AMQP 1.0 plugin enabled

## 📄 License

MIT License - Based on the official [RabbitMQ AMQP Python Client](https://github.com/rabbitmq/rabbitmq-amqp-python-client)

## 🙏 Credits

This library is based on the official RabbitMQ AMQP 1.0 Python client by the RabbitMQ team.
