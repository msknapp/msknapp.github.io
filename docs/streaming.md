---
title: 
weight: 10
description: 
summary: 
lastmod: 2025-08-13
date: 2025-08-13
tags: []
categories: []
series: []
keywords: []
---

# Streaming
## Kinesis

Event Sourcing: Every time the state of a record changes, it produces an event which is stored, and we are able to reproduce past states of records from these events.

MOM: Message Oriented Middleware

Kafka - partitioned, replicated, highly available, highly scalable, fast.
Usually a schema assigned to topics.
Supports fan-out
Pubsub
MQS
Kafka
Events: key, value, timestamp, metadata
Producers and consumers asynchronous, no waiting for each other.
Can do exactly once processing.
Zero, one, or many producers and consumers
Messages can be read more than once, they are not deleted once consumed.
Performance not impacted by amount of data stored.
Data has a retention period.
Topics are partitioned to enable scalability.  Partitions are handled by separate brokers.
Events are read in exactly the same order as written.
Topics can be replicated, even across regions or datacenters.
Very fault tolerant due to replication.
Parallelization is accomplished with partitions.

# GCP PubSub

Asynchronous, scalable.
Messages are stored until acknowledged by all subscribers.
Subscriptions must be made.
PubSub can push messages to a subscriber, or
Subscribers can pull messages.
Supports Fan Out, Fan In, etc.
When messages are acknowledged by all subscribers, they are deleted.
Uses per message parallelism.  Messages are leased out.  It keeps track of if a message was processed successfully.  Therefore messages do not need to be processed in order within any single partition.
Topics can optionally have schemas in protobuf or avro, pubsub enforces schemas.
Each subscription receives each message.  If there are multiple subscribers for a subscription, then each of them receives a subset of messages.  Each message will be processed once per subscription, not per subscriber application.
Messages are deleted when at least one subscriber has acknowledged receipt of the message for each subscription.
There is an ack deadline.  After that it can be sent again.  Messages may be processed more than once.
Subscribers can negatively acknowledge messages.  Then it can be re-delivered.

I believe PubSub is very similar to Amazon’s Simple Queue Service.

How to have Exactly Once Delivery

# Exactly Once Delivery