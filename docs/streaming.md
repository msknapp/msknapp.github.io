# Streaming

- Quality of Service:
    - at least once
    - at most once: if the subscriber is not listening then the message is dropped
- Publish-Subscribe:
    - one producer, many subscribers or consumers
    - all active listeners will receive the message.  aka fan-out
    - if a subscriber is not listening, the message is not received.
    - there are no processing guarantees
- Request-Reply:
    - Publisher expects a response, could be synchronous or async.
    - the message has a reply subject.
    - responders send the messages to the reply subject
    - there can be multiple responses and later ones get dicarded.
- Queues:
    - there is a group of subscribers, all associated with a queue  y name.
    - each message is consumed by only one member of the queue group. 
    - the member is chosen randomly
    - permits easy scaling up/down
    - core nats has no persistence, hence there is a temporal coupling:
        - if a subscriber is not listening then a message is lost.
    Jetstream: is stateful/persistent
- Durable subscribers:
    - streams capture and store messages.
    - subscribers or consumers can replay messages.
    - they can choose how to replay, based on time, sequence number, etc.
    - can control stream retention by policies.
    - retention: limits, interest, or work queue messages are removed as consumed.
    - jetstream can encrypt messages at rest
    - can store on file, memory, and can replicate.
    - can chooe number of replicas by stream
    - can mirror streams across domains
    - nats supports message acknowledgement, or acking.  can batch publish
    - Flow control
    - can support exactly once.  Publisher provides a unique ID.  Server keeps track of these to detect duplicates.  
      Subscribers use double acknowledgement.


## Kinesis

**Event Sourcing:** Every time the state of a record changes, it produces an event which is stored, and we are able to 
reproduce past states of records from these events.

**MOM:** Message Oriented Middleware

- **Kafka:** partitioned, replicated, highly available, highly scalable, fast.
    - Usually a schema assigned to topics.
    - Supports fan-out
    - Pubsub
- MQS
- Kafka
- **Events:** key, value, timestamp, metadata
- Producers and consumers asynchronous, no waiting for each other.
- Can do exactly once processing.
- Zero, one, or many producers and consumers
- Messages can be read more than once, they are not deleted once consumed.
- Performance not impacted by amount of data stored.
- Data has a retention period.
- Topics are partitioned to enable scalability.  Partitions are handled by separate brokers.
- Events are read in exactly the same order as written.
- Topics can be replicated, even across regions or datacenters.
- Very fault tolerant due to replication.
- Parallelization is accomplished with partitions.

## GCP PubSub

- Asynchronous, scalable.
- Messages are stored until acknowledged by all subscribers.
- Subscriptions must be made.
- PubSub can push messages to a subscriber, or
- Subscribers can pull messages.
- Supports Fan Out, Fan In, etc.
- When messages are acknowledged by all subscribers, they are deleted.
- Uses per message parallelism.  Messages are leased out.  It keeps track of if a message was processed successfully.  
  Therefore messages do not need to be processed in order within any single partition.
- Topics can optionally have schemas in protobuf or avro, pubsub enforces schemas.
- Each subscription receives each message.  If there are multiple subscribers for a subscription, then each of them 
  receives a subset of messages.  Each message will be processed once per subscription, not per subscriber application.
- Messages are deleted when at least one subscriber has acknowledged receipt of the message for each subscription.
- There is an ack deadline.  After that it can be sent again.  Messages may be processed more than once.
- Subscribers can negatively acknowledge messages.  Then it can be re-delivered.

I believe PubSub is very similar to Amazon’s Simple Queue Service.

## Exactly Once Delivery

How to have Exactly Once Delivery
