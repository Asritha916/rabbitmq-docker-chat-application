# RabbitMQ Docker Chat Application

A lightweight terminal-based chat application built with **Python**, **RabbitMQ**, and **Docker**. The application enables communication between Docker containers using RabbitMQ as the message broker while maintaining a persistent chat history.

## Features

* Real-time messaging between Docker containers
* RabbitMQ-based message broker
* Persistent chat history
* Automatic RabbitMQ connection retry mechanism
* Standalone mode for offline usage
* Multi-threaded message listener
* JSON-based message format
* Configurable through environment variables

---

## Technologies Used

* Python 3
* RabbitMQ
* Docker
* Pika (RabbitMQ Python Client)
* JSON
* Multithreading

## How It Works

The application consists of two major components:

### Sender

* Reads user input from the terminal.
* Creates a JSON payload containing the sender name and message.
* Publishes the message to the target RabbitMQ queue.

Example payload:

```json
{
    "sender": "Alice",
    "message": "Hello!"
}
```

### Receiver

A background listener thread continuously:

* Waits for incoming messages.
* Decodes the JSON payload.
* Displays the message in the terminal.
* Saves the message to the local chat history.

---

## Environment Variables

| Variable          | Description                  | Default        |
| ----------------- | ---------------------------- | -------------- |
| `RABBIT_HOST`     | RabbitMQ server hostname     | `localhost`    |
| `QUEUE_NAME`      | Queue this client listens to | `chat_queue`   |
| `TARGET_QUEUE`    | Queue to send messages to    | Required       |
| `USER_NAME`       | Username displayed in chat   | `unknown-user` |
| `STANDALONE_MODE` | Enables offline mode         | `false`        |

---

## Chat History

All sent and received messages are stored in:

```text
/app/data/history.txt
```

When the application starts, previous chat history is automatically displayed.

---

## Standalone Mode

If RabbitMQ is unavailable or standalone mode is enabled, the application switches to local mode.

Features include:

* Local message logging
* Persistent history
* No network dependency
* Useful for testing and debugging

Enable standalone mode:

```bash
STANDALONE_MODE=true
```

---

## RabbitMQ Connection Handling

The application automatically attempts to reconnect if RabbitMQ is unavailable.

* Maximum retries: **5**
* Retry interval: **2 seconds**
* Falls back to standalone mode if the connection cannot be established.

---

## Running the Application

### Install Dependencies

```bash
pip install -r requirements.txt
```

### Start RabbitMQ

If using Docker:

```bash
docker run -d --hostname rabbit --name rabbitmq \
-p 5672:5672 \
-p 15672:15672 \
rabbitmq:3-management
```

RabbitMQ Management Console:

```
http://localhost:15672
```

Default credentials:

```
Username: guest
Password: guest
```

---

### Run the Chat Client

Example:

```bash
export USER_NAME=Alice
export TARGET_QUEUE=bob_queue
python app.py
```

Run another instance:

```bash
export USER_NAME=Bob
export QUEUE_NAME=bob_queue
export TARGET_QUEUE=chat_queue
python app.py
```

Both instances can now exchange messages through RabbitMQ.

---

## Message Flow

```text
User Input
      │
      ▼
JSON Payload
      │
      ▼
RabbitMQ Queue
      │
      ▼
Receiving Client
      │
      ▼
Terminal Display
      │
      ▼
History File
```

---

## Error Handling

The application includes:

* RabbitMQ connection retry logic
* JSON decoding exception handling
* Graceful shutdown using `Ctrl + C`
* Automatic fallback to standalone mode if the broker is unavailable

---

## Learning Outcomes

This project demonstrates:

* Inter-process communication using RabbitMQ
* Producer–Consumer messaging architecture
* Docker-based distributed applications
* Python multithreading
* Environment variable configuration
* Persistent file storage
* Asynchronous message processing
* Fault-tolerant application design

---

## Future Improvements

* Private messaging
* Multiple chat rooms
* Message timestamps
* User authentication
* End-to-end encryption
* Graphical user interface (GUI)
* Message delivery acknowledgements
* Broadcast messaging
* Docker Compose deployment for multiple clients
