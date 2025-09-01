
# Cloud

## Platforms

- IaaS: infrastructure as a service.  You allocate servers with CPU, memory, network bandwidth, etc.  Mount volumes or disks.  
  Initialize them yourself.  You don’t deal with the physical maintenance of the server, no installation, cooling, no physical security.
- PaaS: platform as a service.
- SaaS: software as a service.  You don’t manage servers, the operating system, maintenance, upgrades, etc.  You just configure how a 
  service should work, then let it run.
- Serverless: You don’t setup servers, nor the OS, nor install software.  You just bring the application or code.  Not billed unless in use. 
  Often scales well.  Far easier to maintain.
- Auto scaling
- Requires stateless services, or sticky sessions.
- Load Balancing
- Round Robin
- Metric based, number of transactions, 
- Health checks

## Amazon Web Services

## Identity Access Management (IAM)

- Principals:
    - User: root, IAM
    - Role
    - Federated User
    - Application
- **Federation:** using a third party identity provider.  The provider gives AWS your credentials.  
  Usually involves active directory and LDAP.  Okta and Microsoft Entra are also supported.
- Request Context:
    - One of:
        - **actions:** from the web console
        - **operations:** from the CLI, API, or SDK.
    - **Resource:** the object being acted upon.
    - **Principal:** The person or application making the request
    - **Environment Data:** IP, user agent, ssl status, timestamp
    - **Resource Data:** more specific information about the resource being acted upon, such as a specific instance, table, topic, etc.
- Policy types:
    - **Identity based:** Most common.  Assigns authenticated users access to resources.
    - **Resource based:** Less common.  Enables access across accounts.
- Policy Evaluation:
    - For root user, defaults to allow access.  For all other principles, it defaults to deny a request.
    - If any policy denies the request, no other policy can approve it, it stops processing and denies it.
      All policies must allow the request for it to be approved.
    - If a policy allows the action, that changes the default decision.  It will continue evaluating other 
      policies until the request is denied or not.  In short, a request must be explicitly allowed by at least one
      policy, and not denied by any other policy.
- You should not use the root user except to setup other users and roles.
- It's preferred to have users assume a role to get access to resources, because the credentials are temporary.
- **Entity:** a user or role
- **Roles:** are an identity with a set of permissions.  Human users assume a role.
- Human users get long term credentials
- Roles and Federated users are given temporary credentials.  AWS recommends always requiring temporary credentials.
- IAM users should only be created when an application cannot work with roles.
- **Entity:** IAM User or Role.  A person or application authenticates themself as an Entity.
- **Group:** A collection of IAM users.  Policies can be assigned to groups so all users have them.
- **Identity:** IAM Entity or Group.  Multiple policies can be assigned to an identity, they get merged.
- Identity Based Policies: are attached to a user or group.
- Resource based policies: are attached to a resource like a s3 bucket.  
- Federated users are not IAM users, they must assume an IAM role.
- Roles support a resource policy and an identity policy.  
    - Their resource policy is called a role trust policy, and it specifies what entities are trusted to assume that role.
    - Their identity policy states what actions that role may perform an what resources and under what conditions.
- Policies:
    - **inline:** defined directly on a user, group, or role.  They cannot be re-used elsewhere.  These are discouraged.
    - **managed:** Defined separately.
        - **AWS managed:** AWS authors and maintains these, they are simpler to use.  You cannot change them.
        - **customer managed:** You write these, they offer more control, but require more work on your part.  This is
          what I'm most interested in.  Typically you will be writing customer managed identity based policies that are
          assigned to roles.
- [Policy Format](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#access_policies-json):
    - version: specifies the version of the policy format.  Just use the latest, "2012-10-17"
    - statements: a list
        - **Effect:** Allow or Deny
        - **Principal:** only allowed in resource based policies, specifies what identity this applies to
        - **Action:** a list of actions, depends on the resource.  Can be a single string, or a list of strings.
          Values can use wildcards like "*" or "?"
        - **Resource:** What resource this policy applies to.  For resource-based policies, this might not be needed or allowed,
          depending on what resource it gets attached to.  Can be a single string or a list of strings.
          Values can use wildcards like "*" or "?".  These refer to ARNs.
        - **Condition:** states under what condition this policy is enforced.
    - The [policy reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) is also a 
      great resource.

### Data Storage

- Data lakes are raw, unprocessed, not ready for use, but may have clean data.  You process the data upon reading or exporting it.
- Datastores have a well defined schema
- Data warehouses are ready for use, and structured.  You process the data before writing or importing it.
- NoSQL databases do not let you set relational mappings and enforce them between tables.
- S3 has a 5 TB file size limit.
- Redshift is a data warehouse.
- Spectrum uses redshift backed by S3, so you query S3
- **TimeStream:** for time series databases.
- **DocumentDB:** used for migrating from MongoDB
- **DMS:** data migration service, transfer between relational databases, but does not allow any complex transformations.
- **Homogeneous:** same database types, but maybe on different versions.
- **Heterogeneous:** different database types.
- DMS manages the resources for you.
- **Glue:** An ETL tool (extract, transform, load).  It has crawlers and classifiers.  You can change the format.
- Usually the goal in using Glue is to get data into S3 in a structured file format.
- **Athena:** serverless, S3 queries, doesn’t need a redshift cluster, but does need a glue table.
- Data can be static or streaming.
    - Static: already acquired, not changing, not constantly being updated.
    - Streaming: constantly being updated, UCI, OpenData, Kaggle, BigQuery
- Data Analytics is for realtime SQL

### Virtual Private Cloud

- isolates a set of virtual machines.

### Elastic Compute Cloud

- virtual machines as a service.
- billed to the hour.
- many different types.  Ratios of cpu to memory vary.  These affect bandwidth too.

# Lambdas or Functions

No server or operating system maintenance.

### Elastic Block Store

- attaches to EC2 instances, providing persistent storage.
- hard disk or solid state drive.

### Elastic Kubernetes Service

- a managed service for Kubernetes clusters.

## SQS

- simple queue service
- reliably asynchronous publishing of messages to a queue
- queues: destination, temporary holding.
- publish to is, can process later
- messages: blobs, limited size
- polling: subscriber method of retrieving
- standard queues: order is not guaranteed.  At least once delivery
- FiFo Queues: first in, first out, exactly once processing is guaranteed.
    - order is preserved.  Limits velocity
- queues let you decouple data sources from processing
- typically the polling app owns the queue.
- messages sit in queue util polled or read.
- lambdas automatically poll
- ideally subscribers are registered before any mesages are sent.
- can batch poll
- when messages are polled, they are temporarily hidden from anything else oplling
- standard queues don't respect order.  fifo does
- but limits publish tps, Gives freedom in speed that theya re read.
- queues ar more of a pull model.

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

### Kinesis

- Also has datastreams, firehose, data analytics, and video streams.
- There are data producers.  The data streams are in shards.
- Consumers could be lambda, ec2, or kinesis data analytics.
- Shards are a wrapper or container, from 1 to 500.
- There is a unique partition key and a sequence number.
- There is a 1mb payload max.  It’s transient, 24hours to 7days.  Can receive a max of 1000 records per second.
- Firehose is meant for getting data into storage as soon as possible, there is no retention built into it.  
  It has no shards, it can pre-process data with lambda, optionally.  It sends things to storage.
- Datastreams are meant more for immediately acting on each record, but it has data retention, you may not store data outside of it.
- **Video streams:** live video, audio, pictures, radar
- **Data analytics:** lets youtube SQL on incoming data, it’s a consumer from kinesis data streams or firehose.  Can send to s3, redshift, 
  or can view in realtime.  Can be used to create metrics, or send alerts and/or alarms.  Can be used to pre-process for redshift.
- Data Pipeline is meant more for databases

### Relational Data Store

- Managed relational databases.
- Supports postgres, mysql, sql server, and some others.
- aurora improves their speed by using a distributed, replicated, SSD storage layer.

### DynamoDB

- Distributed NoSQL document database.
- See [Dynamo](data#dynamodb)

### Redshift

- Data warehouse.  Columnar storage.  Analytics jobs.  Business analytics.
- Supports SQL

### Cloudwatch

- metrics, logs, traces.

### Managed Service for Kafka

### Route53

- DNS service.

### Cloud Gateway

## AWS Glue

- used for data preparation
- scala or python
- can schedule
- ETL services
- structured or unstructured
- crawls input data, infers a schema
- creates a data catalog
- can setup scala or python jobs to do transformations
- can generate code for you and let you edit it in their console.
- can run or schedule jobs, or have them be triggered by events from s3, dynamo, rds, rs, db on ec2, etc.
- can output to s3, emr, athena, or rs.
- has databases and crawlers.
    - databases have tables and connections
    - crawlers use classifiers to create tables.
- jobs can have a type: spark (creates a cluster) can use python or scala
    - can let glue generate a script for us in pyspark, which comes with many built-in transformers.
    - can do python shell" and edit it in console. 
    - python libs are limited to a set, including numpy, pandas, and sklearn.
- glue lets you create Zeppelin and Jupyter notebooks to do trasformations.  Not linked into jobs, but good for ad-hoc work.
- notebooks created from glue are hosted in sagemaker
- sagemaker lets you create jupyter notebooks.
- Most common py libs are already installed, but you can install more.
- Glue notebooks: long standing, repetitive
- Sagemaker Notebooks: quick ad-hoc
- EMR is also used for data prep.  It is hadoop, fully managed.  Use EMR when you have Petabytes of data.
- using spark from EMR or SM means you don't need to manage the EMR infrasturcutre quite as much.
- you can run hive and spark from jupyter notebooks in EMR
- Athena: serverless, depends on data catalog, queries s3 data.  Use athena if you only want to use sql.
- data pipeline: process and move data between data sources, can do things in no-scala/python languages.
  usually not used for data prep.  Use DP if you need a different language.

### Mechanical Turk

Appears to be a machine, but is actually people on the inside.  You can enable people to label data for a classification algorithm,
as part of its training or test data.

### SageMaker

- Can submit to it by four means: Jupyter, SDK, CLI, or Spark.

### Machine Learning

- row=observation=event, column=feature=attribute
- label=tag, like a decision has been derived and assigned to the record.
- Data can be labelled or unlabelled, structured, semi-structured, unscructured
- **Sagemaker groundtruth:** Enables you to develop trusted datasets.  Dataset includes both training and test data.
- **Mechanical Turk:** people manually update/label rows/observations
- **Ground Truth:** data that is labelled and trusted, assumed to be correct.
- There is supervised and unsupervised learning.  You generally want 10x more observations than features in a model.
- Categorical vs. continuous features.
- If a data type is not comparable then it is categorical.
- Continuous data can fit on a number line.
- When you bucketize a continuous feature, it becomes categorical.
- **Unsupervised** means the input data does not need to be labelled.
- Algorithms can be registered in Amazon marketplace and sold to others or made public.

## Google Cloud Platform

TODO 

## Building An Awesome Almost Free System

The goal here is to have an awesome service or system that is very cheap to run.  You should avoid anything that has a base rate even 
when it is not used, like virtual machines.  Prefer serverless things like functions/lambdas, FireStore/DynamoDB, API Gateways, Cloud 
storage/S3, etc.  You can setup a lot of services with no base cost.  It’s easier to say what to avoid.

Avoid:

- Virtual machines
- Relational databases
- Batch Processing clusters, e.g. hadoop 
