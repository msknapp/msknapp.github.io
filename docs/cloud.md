
# Cloud

## Machine Learning

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

## Mechanical Turk

Appears to be a machine, but is actually people on the inside.  You can enable people to label data for a classification algorithm,
as part of its training or test data.

## SageMaker

- Can submit to it by four means: Jupyter, SDK, CLI, or Spark.

## Data Storage

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

# SNS


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

# Building An Awesome Almost Free System

The goal here is to have an awesome service or system that is very cheap to run.  You should avoid anything that has a base rate even 
when it is not used, like virtual machines.  Prefer serverless things like functions/lambdas, FireStore/DynamoDB, API Gateways, Cloud 
storage/S3, etc.  You can setup a lot of services with no base cost.  It’s easier to say what to avoid.

Avoid:

- Virtual machines
- Relational databases
- Batch Processing clusters, e.g. hadoop 

## Docker

- lightweight, isolated runtime environment.  Includes code, runtime, dependencies, libraries, tools, filesystem, etc.
- running image is called a container.
- images do not include a kernel, they reuse the one from the host.  Operating systems are not needed.  This makes them much lighter.
  and faster to download and boot.
- by having an isolated environment, you can more easily port your application to other host machines.
- virtual machines virtualize the hardware, and include an operating system.  Containers virtualize the operating system.
- docker daemon: builds, runs, and distributes containers (publishing to a registry).  manages images, containers, networks, and volumes.
- docker compose: lets you run a set of containers together.
- images:
    - read-only template for containers.  They are immutable.
    - composed of layers, each one adds a change, like installing an application.
    - can be published to a registry or downloaded from one.
    - are versioned (tagged).
    - when an image is re-built, only layers that have changed get re-run or re-built, and any layers after them.
- containers:
    - can have storage attached, or connect with a network.
    - uses a union filesystem so it sees all files and directories in the image layers.
    - a read-write filesystem layer is added to the top at runtime, so the app can make changes. 
    - can be started, stopped, started again.  Data written in its prior run is still available.
    - if the container is _removed_, then only the changes written to an
      attached persistent storage will be saved.  Anything written to its local read-write volume are deleted.
- if you try running a container for an image that doesn't exist, docker will pull it from a registry, if possible.
- Written in Golang, uses "namespaces" for isolation.
- **registry:** a centralized location that stores container images.
    - registries hold repositories, and repositories hold images.  Images have versions.
    - Most registries also have namespaces that hold the repositories.  These correspond with an organization, team, or group.
    - Dockerhub, AWS elastic container registry (ECR), Google Container Registry (GCR), Artifactory, and Harbor are registries.
    - Dockerhub lets you create public repositories, and one private, for free.
- multi-stage builds: let you build in one stage, and copy the results into another for the final image.  Dependencies that were 
  only needed at build time will not exist in the final image.  This makes it more light weight and also more secure by having fewer
  things installed.
- Writing Dockerfiles:
    - FROM `image`: the base image
    - RUN `commands...`: a command or set of commands
    - EXPOSE `port`: declares that a container uses a port internally, does not actually _publish_ it.
    - WORKDIR `path`: sets the working/current directory.
    - ENV `key value`: sets an environment variable.
    - COPY `from to`: copies a file or set of them from the host to the image.  You can use `--chown` and/or `--chmod` here too.
      Use `--from=` to specify a past build stage to copy from.
      Use `--link=true` to make the layer not be invalidated by changes to previous layers.  This gives you faster builds.
      Using `--link=true` will not work if the destination path contains a symbolic link.  Otherwise, `--link=true` should always be used.
    - ADD `from to`: for most normal files, this acts like copy.  Here are the exceptions:
        - for archives: it will extract them.
        - for git repository URLs: it will git clone them, omitting the `.git` directory unless you pass `--keep-git-dir=true`.
        - for other URLs: it will download them.  You can pass a `--checksume=...` argument for more security.
    - USER `id`: sets the user in future commands and at runtime.
    - CMD `string` or `["cmd", "arg1", ...]`: Sets the default runtime command when the container starts.  Optional.
    - ENTRYPOINT: If set and a container is started with arguments, the arguments are passed to the entrypoint command.
    - ARG `key[=value]`: defines an argument that can be set in the `docker build` command, and optionally sets a default value.
      You can set them at build time by passing `--build-arg key=value`.
    - LABEL `key=value ...`: sets metadata on the image, which can be found by inspection.  You can search for images based on these.
- Boot command:
    - if the `--entrypoint` argument is used with `docker run`: it runs the entrypoint command given on the command line.
      Any extra arguments passed to the container become arguments to the entrypoint command.
    - if no entrypoint is set:
        - if no arguments are provided: it runs all of what was set in the CMD.
        - if arguments are provided: it runs that as _the_ command and its arguments.
    - if an entrypoint is set:
        - if no arguments are provided: it runs all of what was set in the ENTRYPOINT.  If the image also defined a CMD, those
          values become arguments to the entrypoint command.
        - if arguments are provided: it runs the entrypoint defined in the Dockerfile, and passes those arguments as
          arguments to _the_ entrypoint command.
- Understanding Ports:
    - **Publishing:** means that external processes can connect to a container's port.
    - Using the `EXPOSE` command in a Dockerfile does not publish that port.  It's more like documenting that the port is available.
    - Ports can only be published when you run the container, by using -p, -P, or --publish-all.
        - `-p [host-port:]container-port`: lets you publish a port from the container, optionally specifying what port should
          be used on the host as well.  If you don't specify a host port, it chooses an ephemeral one you can discover later.
        - `-P` and `--publish-all` publish all ports that were EXPOSED in the Dockerfile.
    - You can publish ports that were never exposed, and you can expose a port that is never published.
    - It is not necessary to publish or expose a port if it will be connected to by another container in the same network.  It is
      only necessary to publish a port if it must be connected to from outside its own network.
- Runtime Arguments:
    - `-p [host:]internalport` to publish a port.
    - `-v <hostdir|volumename>:<containerdir>[:r{o|w}]` adds a volume _or_ a bind mount.  Can specify read-only or read-write.
      Docker recommends always using `--mount` instead because it has more controls.
    - `--mount type=<type>,{source|src}=<source>,{destination|dst}=<destination>[,options...]` mounts a binding or volume or tmpfs.
        - type can be "volume", "bind", or "tmpfs"
        - 
    - `-e key=value` sets an environment variable, or use `--env-file .env` to pass many environment variables from a `.env` file.
    - `-i` interactive
    - `-t` terminal
    - `-d` run as a daemon, in the background
    - `--rm` when it stops, automatically remove the container.
    - `-u` set the user
    - `-w` set the working directory.
- Understanding Volumes:
    - persistent filesystem storage.  Even if a container is stopped and removed, the volume can remain.
    - These are stored under docker's home, they don't map to directories a user provides.
    - volumes get names, you attach them in the docker run command using `-v <volumename>:<directorypath>`
      The left side can either be a named volume, or a path to a directory or file on your local host filesystem.
      If the left side is a valid volume name, that is used, otherwise it's treated as a file path for a bind mount.
    - These can be attached to multiple containers on the host simultaneously, offering a means of sharing data.
    - They usually start empty, like if you `docker volume create <volumename>` it is empty.  
    - If an empty volume is mounted to a directory that already contains files, those files get copied into the volume.
    - If you `docker run -v <name>:<dir>` with a volume name that does not already exist, it will be created automatically.
      Therefore, running this way is also an easy way to pre-populate a new volume.
    - Getting data out of a volume onto the host may require attaching it to a container first.
- Understanding [bind mounts](https://docs.docker.com/engine/storage/bind-mounts/):
    - These attach or mount files/directories from the host machine onto the image.
    - They are ideal for adding configuration, secrets, and/or data inputs from the host.
    - Use volumes if you need filesystem changes to persist even if the container is removed, 
      or if you need to share filesystem data between multiple containers.
      Use bind mounts if you need to pass configuration from the host.
    - Mounting just one file: the path on the host must be absolute, not relative, or it won't work.
- tmpfs mounts will be deleted as soon as the container stops.  Its data won't even exist if the container is restarted.  Use them if
  you need a large file buffer for temporary processing.
- Understanding User and Group IDs:
- Understanding [Multi Platform builds](https://docs.docker.com/build/building/multi-platform/):
    - Enables one image to be built for different operating systems, and CPU architectures (amd64 vs arm64).  Otherwise an emulator is
      needed to run the image, making it slow.  Typically we just worry about linux amd64 and arm64.
    - the docker pull command automatically selects the image for your own platform.
    - docker desktop has a setting you can enable for it to use containerd, which means it supports multi platform builds.  Otherwise you
      need to create a custom builder image using `docker buildx create ... --use`.
    - `docker buildx build --platform linux/amd64,linux/arm64 .`
    - Referring to the platform in the Dockerfile: use [environment variables](https://docs.docker.com/build/building/variables/#multi-platform-build-arguments)
      that are automatically defined in buildx.  Usually `TARGETARCH` is what you want.
- Making better images:
    - Consider a more light-weight base image.
    - Consider separating them into stages so the runtime image has only what is needed.
    - Consider setting a non-root user that has minimal privileges.
    - Consider adding labels to them.
    - Don't copy in configuration at build time, do that at runtime.  It's more maintainable to have images that are unlikely to 
      need changes.
    - Reduce environment variable use.  
    - Put binaries where they already are on the path.
    - Build separate images for debugging that have tools you need.  Use them in non-prod as necessary.  Or add 
      debug tools in a sidecar.
    - expose ports for documentation reasons.
    - Add `--link=true` to copy or add commands.  Avoid using symbolic links.
    - Set a custom prompt PS1.
    - Support different architectures.
    - Automate their build from triggers like git commits.
    - Set a reasonable working directory.
    - Simplify or minimize the application.
    - reduce permissions for containers to modify files on the host or in volumes.
    - minimize the scope of containers, don't have them be responsible for running multiple applications, use multiple containers.
    - for testing, don't include compiled application binaries into images, mount them instead.  Since they change frequently,
      you can save build time this way.
