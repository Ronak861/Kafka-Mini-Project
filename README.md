# Kafka Mini Project

A minimal Node.js example demonstrating event-driven communication using [Apache Kafka](https://kafka.apache.org/) and [KafkaJS](https://kafka.js.org/). It simulates a simple **order → payment** flow: a producer publishes an order event, and a consumer picks it up and "processes" the payment.

## How it works

1. **Producer** (`producer.js`) connects to Kafka and sends an order event to the `orders` topic.
2. **Consumer** (`consumer.js`) subscribes to the `orders` topic, listens for new messages, and runs a (dummy) payment processing function for each order it receives.
3. **Kafka broker** (`docker-compose.yml`) runs locally via Docker using Confluent's `cp-kafka` image in KRaft mode (no Zookeeper required).

```
producer.js  --sends order-->  [orders topic]  --consumed by-->  consumer.js --> processPayment()
```

## Tech Stack

- Node.js
- [kafkajs](https://www.npmjs.com/package/kafkajs) — Kafka client for Node.js
- Docker Compose — for running a local Kafka broker (Confluent Kafka image, KRaft mode)

## Project Structure

```
KAFKA/
├── kafka.js            # Shared Kafka client instance (clientId + broker config)
├── producer.js         # Publishes an order event to the "orders" topic
├── consumer.js         # Subscribes to "orders" and processes each message
├── docker-compose.yml  # Local single-broker Kafka setup
├── package.json
└── .gitignore
```

## Prerequisites

- [Node.js](https://nodejs.org/) (v16+ recommended)
- [Docker](https://www.docker.com/) and Docker Compose

## Setup & Usage

**1. Clone the repo**
```bash
git clone https://github.com/Dagar214/Kafka-Mini-project.git
cd Kafka-Mini-project
```

**2. Install dependencies**
```bash
npm install
```

**3. Start Kafka**
```bash
docker-compose up -d
```
This spins up a Kafka broker on `localhost:9092`.

**4. Run the consumer** (in one terminal — it listens continuously)
```bash
node consumer.js
```

**5. Run the producer** (in another terminal — sends one order event)
```bash
node producer.js
```

You should see the consumer terminal print the received order and a "processing payment" log.

## Example Output

**Producer:**
```
✅ Producer connected to Kafka!
📤 Order event sent successfully!
```

**Consumer:**
```
✅ Consumer connected to Kafka!
📥 New message received!
   Topic:     orders
   Partition: 0
   Offset:    0
   Order:     { orderId: 'ORD-001', item: 'Laptop', price: 999, status: 'placed' }
💳 Processing payment for order ORD-001
```

## Notes

- `processPayment()` in `consumer.js` is a placeholder — replace it with real payment logic (e.g. charging a card, updating a database).
- The consumer subscribes with `fromBeginning: true`, so it will replay all existing messages in the topic on startup.
- Kafka broker configuration (ports, listeners, cluster ID) is defined in `docker-compose.yml` and can be adjusted as needed.

## Stopping Kafka

```bash
docker-compose down
```
