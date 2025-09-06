
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

### Shared Responsibility Model

- AWS handles physical security of infrastructure,
- you are responsible for securing things you put on it.
- strong physical security
    - two factor authentication, 2x
    - restricted access to those with a need
    - video surveillance
    - fire protection
    - uninterruptible power supply (UPS), they have backup generators.
    - temperature and humidity controlled.
    - storage devices are decommissioned, prevents data loss.
    - no cold data centers.
- AZs are fed by different electrical grids
- AZs in low risk flood plains
- unmarked buildings
- propietary DDoS prevention techniques are employed
- not possible to spoof IPs from an EC2
- port scanning is a violation of the terms of service
- impossible to sniff packets from the host, hypervisor won't deliver the packets, even in promiscuous mode
- virtual MFAs, less secure than hardware tokens.
- API calls require a digital signature from a SAK, prevents replay attacks because there is a timestamp, if it's over
  fifteen minutes old then it is denies.
- it is impossible to alter cloudtrail logs without being detected.
- Xen hypervisor with paravirtualization, no elevated access to CPU.
  guest OS in ring one, host in zero.
- AWS firewall between physical network interface and virtual, in hypervisor layer, so all traffic (even between instances on the same machine) must go through it.
- only presented with virtual disks.  Memory is scrubbed when unallocated.

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
- notebook:
    - roles use STS, 15 minutes to 36 hours
    - EC2s use roles (should)
    - roles can be used to grant temporary access to other people or accounts.
    - federation: grant permissions to users by a trusted external system.
    - roles for EC2 removes the need to store credentials on an EC2
    - federation:
        - OIDC: facebook, google, amazon, etc.
        - SAML: active directory, LDAP (security assertion markup language)
    - IAM can limit users to console or CLI access
    - policy: effect (allow/deny), service (s3, ASG, etc), resource, action (read/write/delete, etc.), conditions (if...)
    - there are user policies and managed policies
    - mfa: can have two access keys at one time for rotation
    - when conflicting policies apply
        - explicit denies win
        - then explicit allows
        - otherwise denied
    - with assumed role: a policy cannot expand the permissions of a role.
    - IAM cannot restrict access by state
    - IAM cannot provide CAPTCHAS.

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

### Simple Storage Service

aka S3.
- three storage classes: general purpose, infrequent access, archive
- lifecycle policies let data auto migrate storage class
- glacier: cheap but very slow to access
- buckets can have unlimited data/objects
- can't operate on just part of an object, must write all of it at once.
- data is automatically replicated
- auto partitions for high request rates.
- 5 TB max object size
- namespaces are global but buckets are associated with regions
- 100 buckets per account by default.
- not really a file system.
- bucket data lies within one region unless explicitly copied.
- tags can only be assigned at object creation
- buckets don't really have folders, the console just treats it that way.
- there are just keys in one namespace and no structure.
- can sustain loss of two AZs
- RRS: reduced redundancy storage: is cheaper for easily reproduced data.
- can require mfa to delete, only root can enable this.
- can replicate across regions, can version.
- puts to new objects can't be stale
- updates are atomic
- bucket policies can grant access to other accounts.
- ACLs are legacy
- bucket policies are recommended, they are FGAC (fine grained access control)
iam policies also can grant access to s3 and limit it.
- easily supports static website hosting.
- IA: infrequent access, 128kb min size, 30 day min duration
- aws managed keys: envelope encryption with rotation.  all managed by amazon
- SSE-KMS: you manage the keys, separate permissions for master key.  KMS supports auditing.
- pre-signed URLs let you share s3 data with people for a limited time.
- for files over 100MB, use multi-part upload
- for files over 5 GB, you must use multi-part upload
- the CLI automatically does multipart upload.
- you can replicate across regions automatically
- existing objects are not replicated.
- you can download just a range of data
- you can enable access logs in s3, to support auditing.
- supports high request rates, 100 requests per second.
- glacier is very durable and fault tolerant.  It is also associated with a region.
    - it archives 40TB max, is immutable and encrypted.
    - Glacier uses vaults.  you can have 1000 vaults max per account.  vaults contain archives.
    - vaults have lock policys, you can use the s3 interface to interact with glacier.
- s3 can send event notifications.
    - events: RRS lost, object created, deleted
    - destinations: lambda, SNS, SQS
- **durable:** the data exists in a usable form, and you can rely on it still existing in a usable form for the long term.
- **available:** the data can be accessed right this moment, and is likely to be accessable at random moments in the future.
- notebook lessons learned:
    - when versioning eis enabled,if you merge data periodically, it still keeps around all previous ones.  These can still impact performance.
    - s3 partitions are sorted.  if a directory has the date, then all writes go to one partition, resulting in less throughput
    - might need to do deletes of an explicit version of files
    - versioning can only be applied to all of bucket or none
    - I think storage costs get amplified with versioning.
    - not good to mix files that need and don't need versioning in one bucket
    - like don't have tmp files sent to a versioned bucket
    - by having SSE encryption over KMS, not just constrained to s3 speed any more
    - but kms request rates too
    - 1200 requests per second per account, many others using service too.
    - between drill (hadoop DFS) does support glob patterns
      so you could have partitioned s3 directories.
    - s3 throughput is applied per bucket.  100 puts per second, 300 gets per second
    - by writing huge numbers of small files, you diminish throughput.
    - might improve by using endpoints, but not by much.
    - s3 partitions when workload ramps up gradually, not rapidly
    - could try using a lifecycle policy to delete these automatically
    - UI shows size of directory i
    - also charged per request, so having many small files costs more
    - lifecycles can be applied by tags, a key prefix, or both
    - versioning is required for cross region replication, and IAM policies allowing s3 to do the replication
    - SSE-C: with each s3 put/get, you provide an encryption key and s3 does the encryption/decryption for you
    - SSE-S3: S3 takes care of envelope encryption, it stores all keys for you, and rotates the master key periodically (once per month)
        - you can't manage the keys or audit their use.  access is only controlled through bucket policies, not by locking down key permissions.
    - SSE-KMS: uses a KMS CMK to encrypt your objects.  You can in fact control permissions to S3 objects by changing IAM permissions for the CMK.
        - gives you auditability, can see who tried accessing what in s3,
        - downside: must pay for KMS usage
        - you can control when CMKs are rotated.
    - s3 has server access logs to provide records of access to objects.


### Virtual Private Cloud

- isolates a set of virtual machines.
- from /16 to /28
- cidr can't change after creation
- if connected with other vpc, IPs can't overlap
- VPCs can span AZs but are constrained to one region
- subnets can't span AZs
- AWS takes the first four and last one IP of subnets.
- 1 IGW per VPC, not associated with single subnet
- VPN only subnet: route table goes to VPG
- VPCs implicitly have a router
- route tables operate at subnet, but the VPC has a default if one is not assigned.
- when traffic matches multiple rules in the route table, most specific one is followed
- IGW allows communication with internet in VPC.
- IGW performs NAT, for instances with public IPs.
- having an EC2 in public subnet alone does not let it communicate with internet.  for public internet access: vpc / subnet/SG must allow it,
  and the EC2 must have a public IP or EIP.
- EIPs cannot move across regions.
- EIPs can move to other VPCs in same region.
- DHCP: dynamic host configuration protocol
- tells each host what DNS servers to use, what the VPCs associated domain name is, and netbios node type
- can provide DNS servers
- setting a custom domain name requires a custom DNS server
- can change VPC's domain name
- if using IGW, keep amazon's DNS
- only one DHCP/VPC
- ENI: elastic network interface
    - 1 public IP, four private, one primary private
    - can assign to one instance only
    - can be re-assigned, can live beyond life on instance.
    - multiple can be assigned to one instance
    - associated with a subnet
    - can let you have an instance be dual homed
- peering:
    - must be in the same region
    - can be separate accounts
    - requires using DNS hostname resolution
    - requires route table updates
    - CIDR blocks can't overlap, in IPV4, even if you just plan to use IPV6
    - can't be transitive
    - only one between any two VPCs

| direction | IP version | IGW | NAT | EIGW |
| --- | --- | --- | --- | --- |
| ingress | 4 | yes | no | no |
| ingress | 6 | yes | no | no |
| egress | 4 | yes | yes | no |
| egress | 6 | yes | no | yes |

- all three are stateful to allow responses.
- all three support NAT for public vs. private IP mapping
- always horizontally scaled and highly available.

- classiclink: links EC2 classic with a VPC
- endpoint: creates private connections between VPC and an AWS service, like S3 or DynamoDB
    - uses no network hardware (no IGW, no NAT, EIGW, VPN)
    - IPv4 only
    - can't span regions.  VPC and buckets must be in same regions.
    - no extra charges
    - route tables need updating, maps a prefix list (specific to service) to VPCE (virtual private cloud endpoint)
    - could map service's IP specifically, but that can change.

- limited to five VPCs per region by default.
- DHCP options set resolves the DNS, the IGW does the forwarding.
- VPC uses IPSec protocol, not SSH
- EIP on stop/start: classic is disassociated, VPC remains associated.
- ENI is not detached by a stop of an instance
- "underlying host" refers to physical server.  When an EC2 is stopped and restarted it can be spun up anywhere, so the host almost always changes.

### Security Groups

- stateful firewall on instance, controls ingress and egress
- specify destination, protocol, and port
- for UDP and TCP
- you can change them without stopping the instance
- 500 per VPC, 
- 50 rules per SG
- response traffic is always allowed
- allow rules only.  The SG is defaulted to deny
- can associate with a network interface.
- default: allows all outbound traffic, only allows inbound with same SG
- for an EC2 instance outside of a VPC, you cannot change its SG after launch.

### NACL

- subnet level, stateless
- responses need explicit permission
- both allow and deny rules, by priority.

### Trusted Advisor

- tells you how you can save money, improve security, improve fault tolerance, availability, or performance.

### AWS Config

- stores a history of resources and how they were configured
- good for auditing, change tracking, and to troubleshoot problems
- can limit it to resource types in region
- can compare state with rules and send SNS notifications when rules fail.
- changes to resources can trigger SNS
- adds auditability
- records cloudtrail IDs with changes.

### Beanstalk

- can submit app code
- don't have to manage ELBs, ASGs, servers, networking, etc. patching and OS upgrades are done for you
- monitoring is done for you
- code kept in S3, code is versioned
- supports web app tiers and/or worker tiers, so not necessarily a web app
- specify env, tier, and platform at start
- supports java, python, ruby, go
- platforms: tomcat, docker
- passenger: web server for ruby: so is puma, also from phusion
- node: maybe is its own platform
- does health checks for you
- manages firewalls (SGs) for you
- retain control of underlying resources, can still SSH to your resources and control them.

### Elastic Compute Cloud

- virtual machines as a service.
- billed to the hour.
- many different types.  Ratios of cpu to memory vary.  These affect bandwidth too.
- c4: compute, g2: graphics, i2: storage, r4: ram
- enhanced networking: single root IO virtualization, faster network
- elasic IPs can be transferred.
- classic SGs only control outbound traffic
- SG defaults to deny
- SG: stateful, always permits response traffic
- SG at instance.
- bootstrapping with user data, is not encrypted.
- instance metadata.
- can change SGs while the instance runs.
- can change type while stopped.
- 10 tags max per instance.
- spot instances: bid a price, may start/stop whenever
- two minute warning before they stop
- placement groups: nodes get low latency 10gbps network b/w each other, in 1az only
- requires enhanced networking
- instance stores: lost when machine stops.
- when an EC2 with instance storage is rebooted, the data persists, it is only lost if the instance is stopped or terminated.

# Lambdas or Functions

No server or operating system maintenance.

- nodejs, java, c#, python
- triggers: s3 puts, dynamo puts, http requests, api calls (from sdk)
- can be invoked from kinesis, sns, cloudwatch,
- coordinate with step functions
- you specify ram and cpu, iam role, timeout, method name
- event info is passed as an argument
- on-demand invocation is when it's invoked by your app, or manually.
- synchronous invocation is when some other app is waiting on the lambda function to finish.  S3 is always async.  Stream
  services are always synchronous.
- kinesis and dynamo streams always wait on lambda (sync)
- when your own app invokes lambda, it can choose to be synchronous.
- arguments can be primitives, pojos, and streams
- it can return primitives, pojos, and streams
- serialized always as json
- can take a context as second argument for events, lambda also has provided POJOs from services
- when ran async, return type is ignored/dropped
- input stream must be json, out does not have to be.
- can build standalone jars or zips with all jars in /lib, and compiled classes in root.
- lambda supports environment variables, up to 4k per lambda.
- lambda supports function versioning and aliases, don't need to change caller source if lambda is bad.
- environment variables are stored encrypted.
- lambda containers might be re-used, you can cache data in them.
- it can keep database connections open between invocations.  This saves runtime.
- you annot count on container re-use.
- lambda retries twice for async, and can send to a dead letter queue on sqs on failure.
- with API gateway, you can have lambda handle http request/response.
- can create your own event sources for your apps.
- can write half gb to /tmp
- limited to 1k lambdas running concurrently per account.
- jar must be under 250mb
- miust finish in 300 seconds, 5 minutes.
- limited input/output size
- can be triggered on schedule using cloudwatch timers.

### Elastic Block Store

- attaches to EC2 instances, providing persistent storage.
- hard disk or solid state drive.
- replicated within az, not across azs
- magnetic: 1tb max, 100 iops avg, cheap
- GPSSD: 16tb max, 3 iops/gb, or 10kiops max
- provisioned iops: faster EBS IO, but more expensive
- snapshots are incremental, stored in s3 tech, (not where you see it)
- snapshots are constrained to a region, can copy to other regions.
- ebs from snapshot is lazily loaded.
- encrypt via kms.
- redundant within one AZ, but not across AZs

### Elastic Load Balancer

- can route across AZs
- auto-scales
- supports health checks
-  lives in one region
- cross zone load balancing
- connection draining 300 seconds default
- proxy protocol, adds headers, IPs, ports
- sticky sessions use "AWSELB" cookie
- idle timeout 60 seconds
- keep-alive allows connection reuse, reducing CPU utilization
= not multi- AZ unless configured that way.

### Automatic Scaling Groups

- ASGs
- supports health checks
- can schedule scaling
- can do manually
- 100 LC per region
- can use spot bids
- can scale based on cloudwatch metric
- LC: launch config, AMI, instance type, security group, key pair
- 20 ecz limit per region
- to roll out a patch, update the LC, then dereg or terminate instances slowly.

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
- notebook:
    - scales. at least once.  multi-readers/writers
    - order not guaranteed
    - over http, 50 calls/sec/computer
    - cant delete by id, must use receipt handle
    - long polling, wait up to 20 seconds for a messages, reduces cpu load.
    - dead letter queues, for failed messages,
    - can grant/ceny access b/w accts w/ sqs access control.
    - messages return success only when durably stored, zero data loss is possible
    - can use sqs to also send prod data to qa/dev.
    - after a message is read, there is a visibility timeout
    - components must delete processed records
    - supports delay queues up to 15 minutes.
    - 12 hour max visibility timeout
    - up to 10 metadata attributes per message.
    - there are standard and fifo queues.
    - durable subscriptions: a consumer can receive messages they missed while they were down.
    - tracks message acknowledgements.
    - multiple consumers, but only one consumer per message.

| Characteristic | Pub/Sub Topics | Point to Point, Peer to Peer Queues |
| --- | --- |
| received by | zero to many receivers (fanout) | exactly one receiver |
| process time | asynchronous, bulletin board | asynchronous or synchronous, consumer pulls |
| when no receivers | only subscribers at time of message was sent will get a copy | timing is not important, either side can be down. |
| consumers | anonymous or unknown consumers | can be multiple consumers but only one will get a message, you know who consumes |
| acknowledgement | no acknowledgement | message sender gets acknowledgements |
| replay | no replay | automatic |
| order | no guarantees | preserved |


| FIFO | Standard |
| --- | --- |
| exactly once | at least once |
| ordered | unordered |
| limited throughput | high throughput |

Comparing with Kafka:

- kafka does not support visibility timeouts or auto-replay, nor acking messages.  
- this is a big advantage to SQS
- with kafka you can replay messages even if they were processed successfully.
- with kafka the application must handle detecting and replaying dropped records
- kafka supports multiple consumer groups, sqs does not.

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
- notebook:
    - subscribers can be: sqs, http(s), email, sms (text), and lambda.  emaili json too.
    - cloudwatch events can go to sns topics.
    - sns allows fanout.
    - sns messages cannot be recalled.
    - deleted topic names can be reused 30-60 seconds later, depending on how many subscribers there were.
    - SNS can't send to dynamodb, but it can send to lambda, which could in turn send things to dynamodb.

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
- notebook:
    - firehose: very rapid ingestion
    - streams: custom apps, for complex realtime analysis
    - analytics: analyze streaming in realtime with SQL
    - firehose: goes to s3, redshift, elastic search
    - data is sent by API call.

### Elastic Map Reduce

- has EMRFS, stores to s3 so cluster can shutdown
- can run hive, pig, spark, presto
- can use EBS or instance storage
- HDFS on instance storage is fastest
- transient (turn off when done, use EMRFS), or persistent clusters.

### Data Pipeline

- at specified intervals, process and move data
- writes/reads from s3, redshift, dynamo, RDS, databases
- processes on EC2, and on their own data nodes.  no lambda
- supports pre-requisites, can chain them
- limited data format support
- retry logic, SNS alerts n failure
- can run activities on EMR as well
- scheduling
- ideal for routine batch or ETL jobs, not streaming
- activities: pig, hive, hadoop jobs, SQL scripts, bash scripts, EMR jobs, simple copies
- can work with non-RDS databases, anything with JDBC
- supports custom AMIs.

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

### Elastic Cache

- redis: cache db, message broker
- memcached: in memory key value store
- auto recovers from failure
- with redis it allows read replicas and they can be used in failover
- memcached can be sharded
- redis supports data types, lists, sets
- redis can write to disk, memcached can't  hence redis supports snapshots.
- redis can recover with a snapshot or backup.
- redis supports five read replicas, spanning AZs
- redis can be a message broker
- redis supports sorting, leaderboards
- memcached: up to 20 nodes
- redis rep groups are composed of r clusters (one node each)
- each can be horizontally scaled manually
- vertical scaling requires building a new cluster.
- memcached always starts empty, redis can load backups
- memcached has zero redundancy
- replication is asynchronous
- redis backups can run on schedule, in maintenance window, and define retention policies.  Manual snapshots require manual deletion
- use SG, NACL, and IAM to secure.
- redis clusters can only have a single node, but you group clusters to make a replication group.
- clients of elastic-cache can be configured to auto-discover new nodes.
- IAM cannot block connections to elastic-cache, but it can block API calls, and that can make it more secure.

### Cloudwatch

- metrics, logs, traces.
- can use put request to update,
- can trigger scaling, SNS
- basic (5 minutes) vs. detailed monitoring (1 minute)
- doesn't aggregate across regions
- 5000 alarms per account, two weeks retention
- there is a logs agent to auto-send logs to cloud watch.

### Managed Service for Kafka

### Route53

- [Route53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html) is a [DNS service](networking#domain-name-service-dns).
- Supports domain name registration.
- Supports health checks.
- Usually you point DNS records to a load balancer.
- Records are tracked within "hosted zones", one hosted zone is tied to a domain name.
- record names are sub-domains of the hosted zone name.  sub-domains prefix a domain, and are separated with periods.
- alias records: route to s3 buckets, cloudfront, and other special aws resources.  Can route to other records in the same hosted zone.
    - zone apex: the domain name for the root of a hosted zone.  You can have an alias record for it, but not a cname.
    - you can have alias records point to an ELB.
    - You cannot set a TTL on alias records, it uses the TTL of what it points to.
    - If the resource it points to gets a new IP, the alias record is immediately updated and responds with that.  Clients that 
      cached the IP for a TTL though, won't immediately be updated.
    - there is no charge for DNS queries to aliases that point to aws resources.
- cname records can point to domain names outside of their hosted zone, aliases cannot do that.  There is a charge for this query.
- records can use an asterisk as their left-most label, to indicate it is [a wildcard domain](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/DomainNameFormat.html#domain-name-format-asterisk).  It is used for any domain where a more
  specific record does not exist.  This includes for descendent domains.  Asterisks used in other ways are treated as literal characters.
- Supports [routing policies](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html):
    - failover: active/passive failover.  Utilizes health checks.
    - latency
    - geolocation
    - weighted routing: for a/b testing, or possibly load balancing.  There are probably better options.
- It can consider [endpoint health](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-determining-health-of-endpoints.html):
    - can consider request status against an endpoint, cloudwatch alert status, and other health checks.
- amazon application recovery controller (ARC) may have a better means of supporting failover health checks by region.
- notebook:
    - TLD (ICANN), interNIC, whois db
    - hosts, subdomains
    - FQDN (proper ends with a dot)
    - nameservers: zone files
    - TLD registrars
    - SOA: start of authority
    - resolver (by ISP) do most caching, most DNS servers don't cache, just forward
    - UDP: 512 byte max
    - A: DN to IP4
    - AAAA: IPv6
    - CName: alias for common name
    - NS: by TLDs, points to other DNS server
    - PTR: backwards A
    - SPF: sender policy framework, stops spoofing of emails.
    - TXT: stores metadata about a host
    - SRV: service
    - can only use alias record for hosted zone's domain, not a cname.
    - routing options: simple, weighted, latency, failover, geo-location
    - weighted: multiple entries in record sets, each with own weight.  can be multiple records for a single host and type.
    - can't use failover in private hosted zones.
    - aliases can point to ELBs.

### Elastic Load Balancer

There are several types.

- elastic load balancer
- application load balancer
- network load balancer

### CloudFront

- content delivery, over internet, cached at edge servers
- delivered from lowest latency.  not just from s3 static web sites, but from web servers too, even ones not on AWS.
- supports dynamic web pages too, but mainly for static content though.
- supports media streaming via HTTP and RTMP
- distributions, origins, cache control (24 hours, headers, min, max, default TTL)
- origins: s3, ec2, elb, or web site
- can invalidate an object in cache at any time.
- can have more than one origin servers
- cache behaiors can cache different based on resource patterns
- can say that certain resources have one origin, other resources are served by another origin
- i.e. static content from S3, dynamic from your server (ELB)
- cloudfront can support private content
- can have short TTL for dynamic content

### Storage gateway

- on prem: can use AWS for storage
- DL a VM, register it on AWS, mount an ISCSI volume on host.
- gateway cached: all sent to s3, some kept in local storage (recently read is cached locally)
- 32 tb per folume, 32 folumes, 1pb
- can't access it directly on s3, must use the st gateway service
- gateway stored: data just gets backed up to s3 asynchronously, 16 tb volumes, 32 volumes, 512 tb total
- backed up as ebs volumes
- snapshot in s3
- snapshots only record changes
- VTL: virtual tape libraries, lets you store things as if it was a tape drive.  so you don't have to change your procedures.
- AWS has three flavors of directory service:
    - MS AD
    - simple AD,
    - AD connector, proxy to on-prem

### Key Management Store

- generate, story, symmetric keys (envelope encryption, data keys, CMKs, encryption context)
- HSM: hardware security modules
- KMS: keys can never be exported
- HSM: securely stores cryptographic material.

### CloudTrail

- audit logs of AWS services
- delivers log files to s3 buckets
- can go to a "log group" on cloudwatch
- can get SNS notifications upon log delivery
- can have a trail for all regions or one.
- can set lifecycles of cloudtrail logs sent to s3
- approximately a 15 minute lag
- logs sent every five minutes.

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
