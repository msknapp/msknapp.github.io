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

# AWS Notes
## Machine Learning:
- row=observation=event, column=feature=attribute
- label=tag, like a decision has been derived and assigned to the record.
- Data can be labelled or unlabelled, structured, semi-structured, unscructured
- Sagemaker groundtruth: Enables you to develop trusted datasets.  Dataset includes both training and test data.
- Mechanical Turk: people manually update/label rows/observations
- Ground Truth: data that is labelled and trusted, assumed to be correct.
- There is supervised and unsupervised learning.  You generally want 10x more observations than features in a model.
- Categorical vs. continuous features.
- If a data type is not comparable then it is categorical.
- Continuous data can fit on a number line.
- When you bucketize a continuous feature, it becomes categorical.
- Unsupervised: means the input data does not need to be labelled.
- Algorithms can be registered in Amazon marketplace and sold to others or made public.
- Data Storage:
- Data lakes are raw, unprocessed, not ready for use, but may have clean data.  You process the data upon reading or exporting it.
- Datastores have a well defined schema
- Data warehouses are ready for use, and structured.  You process the data before writing or importing it.
- NoSQL databases do not let you set relational mappings and enforce them between tables.
- S3 has a 5 TB file size limit.
- Redshift is a data warehouse.
- Spectrum uses redshift backed by S3, so you query S3
- TimeStream: for time series databases.
- DocumentDB: used for migrating from MongoDB
- DMS: data migration service, transfer between relational databases, but does not allow any complex transformations.
- Homogeneous: same database types, but maybe on different versions.
- Heterogeneous: different database types.
- DMS manages the resources for you.
- Glue: An ETL tool (extract, transform, load).  It has crawlers and classifiers.  You can change the format.
- Usually the goal in using Glue is to get data into S3 in a structured file format.
- Athena: serverless, S3 queries, doesn’t need a redshift cluster, but does need a glue table.
- Data can be static or streaming.
- Static: already acquired, not changing, not constantly being updated.
- Streaming: constantly being updated, UCI, OpenData, Kaggle, BigQuery
- Data Analytics is for realtime SQL

### Kinesis:
- Also has datastreams, firehose, data analytics, and video streams.
- There are data producers.  The data streams are in shards.
- Consumers could be lambda, ec2, or kinesis data analytics.
- Shards are a wrapper or container, from 1 to 500.
- There is a unique partition key and a sequence number.
- There is a 1mb payload max.  It’s transient, 24hours to 7days.  Can receive a max of 1000 records per second.
- Firehose is meant for getting data into storage as soon as possible, there is no retention built into it.  
  It has no shards, it can pre-process data with lambda, optionally.  It sends things to storage.
- Datastreams are meant more for immediately acting on each record, but it has data retention, you may not store data outside of it.
- Video streams: live video, audio, pictures, radar
- Data analytics: lets youtube SQL on incoming data, it’s a consumer from kinesis data streams or firehose.  Can send to s3, redshift, or can view in - realtime.  Can be used to create metrics, or send alerts and/or alarms.  Can be used to pre-process for redshift.
- Data Pipeline is meant more for databases
SNS,
SQS

### Simple Notification Service
- Fully managed message delivery from publishers to subscribers.
- Asynchronous messaging over topics.
- Application to application, or application to person messaging.
- Subscribers can send messages to SQS, Lambda, email, text messages, HTTP endpoints, Kinesis Firehouse, mobile push messages 
  (goes to phone apps), or other services.
- Firehose is not just for S3, it can also export to Redshift, OpenSearch, MongoDB, DataDog, Splunk, NewRelic, and more.
- Attributes can be added to a message.
- Subscribers can setup filter policies using one of two scopes, either based on message attributes, or by payload body fields.
- It has complex retry policies for message delivery when the subscriber is unavailable.  It can retry for six hours to send to users, 
  and 23 days for application.  For HTTP subscribers it can retry for up to an hour max.
- It supports dead letter queues, which are hosted on SQS.
- You can create high throughput FIFO SNS topics.  Messages are guaranteed to be delivered in order and only once.

# Platforms

- IaaS: infrastructure as a service.  You allocate servers with CPU, memory, network bandwidth, etc.  Mount volumes or disks.  
  Initialize them yourself.  You don’t deal with the physical maintenance of the server, no installation, cooling, no physical security.
- PaaS: platform as a service.
- SaaS: software as a service.  You don’t manage servers, the operating system, maintenance, upgrades, etc.  You just configure how a service should - work, then let it run.
- Serverless: You don’t setup servers, nor the OS, nor install software.  You just bring the application or code.  Not billed unless in use. 
  Often scales well.  Far easier to maintain.
- Auto scaling
- Requires stateless services, or sticky sessions.
- Load Balancing
- Round Robin
- Metric based, number of transactions, 
- Health checks


# Building An Awesome Almost Free System

The goal here is to have an awesome service or system that is very cheap to run.  You should avoid anything that has a base rate even when it is not used, like virtual machines.  Prefer serverless things like functions/lambdas, FireStore/DynamoDB, API Gateways, Cloud storage/S3, etc.  You can setup a lot of services with no base cost.  It’s easier to say what to avoid.

Avoid:
Virtual machines
Relational databases
Batch Processing clusters, e.g. hadoop 