
# Data Storage

## Concepts

- Three V's of data: volume, variety, velocity.  High volume and variaty is more suitable to NoSQL.
- RPO: recovery point objective.  minutes.  max period of time where data loss could occur
- RTO: recovery time objective.  hours or days.  max time it could take to recover and resume operations from a backup or snapshot.

### Data Lakes

- Data is stored in a redundant and highly available service.
- No schema is guaranteed or enforced.
- Data is crude or unprocessed.  There can be missing values, missing rows, duplicate data, incorrect data.  
  Derived columns and data enrichment is missing.
- **Examples:** AWS S3, GCP cloud storage.

### Data Warehouse

- Holds massive amounts of data, is replicated and highly available.
- Already processed and ingested in a manner that is more ideal for querying.
- Ideal for analytical queries.
- Schemas are defined and enforced for the data.
- May already include feature enrichment, de-duplication, missing value corrections, etc.
- Usually stored in immutable columnar file formats.
- Often used in large batch processing jobs.
- Examples: 
    - AWS Redshift
    - GCP BigQuery.

## Filesystems

- **Network Filesystem:** mounts onto multiple servers, writes by one are seen by others.  
    - Can be used to share data, but is considered relatively expensive.
- **Ext4:** common default.  Does journaling.
- **XFS:** newer, sometimes default.  Does journaling.  Handles larger files better.
- **BTRFS:** B-Tree file system.
- Compression:
    - **Gzip:** Good balance of speed and compression, often comes with linux OS
    - **Bzip:** Slower but can achieve better compression.  More ideal for long term archiving.  Often comes with linux OS.
    - **Snappy:** Google’s compression, claims to be faster than most.  Ideal for transmitting unstructured data, such as in MapReduce jobs.  
      Usually not included for Linux distributions, but can be installed.
    - **BGZip:** block gzip.  Like GZip, but performed in separate blocks of the file at a time.  Ideal for larger files.

## Object Store

### Google Cloud Storage

- Simple Storage Service, Minio
- Immutable objects stored at a path.
- Paths may appear to resemble a file system, but it’s a facade.  Higher directories do not need to exist.
- Usually replicated to be extremely highly available.
- Storage classes let you reduce replication and save money.
- Can be encrypted at rest.
- Can control access to them.
- Often easy to make into a webpage.
- Supports very large files.
- Global.  Buckets are global.
- Files can be versioned.
- Can setup retention policies.

### Simple Storage Service

From AWS, very similar to Google Cloud Storage

## Relational Databases

### Concepts

- **OLTP, OLAP:** online transaction or analytical processing.
    - **OLTP:** designed more for random access, row inserts, updates, deletions one at a time in different places.  Tends to store things as rows.
    - **OLAP:** tends to receive large batches of data periodically, and supports queries against large sets of records, 
      or all records at once.  Mainly for analytics where you query against large amounts of data at once.  Data warehousing. 
      Tends to hold data in column oriented data formats like Parquet, since those perform better for analytics.
- **SQL databases:** structured query language.  
    - Supports atomic transactions.
    - Most are not distributed or horizontally scalable, except via complex sharding.
    - **Postgres:** Open source, written in C, highly capable, extensible, fast.
    - **MySQL, MariaDB:** More user friendly and intuitive than Postgres, though may not perform as well.
    - **SQL Server:** Microsoft’s implementation, popular with Microsoft users.
    - **Spanner:** Similar to Postgres, but is distributed, achieving all three pillars of CAP theorem by taking advantage of a very fast network 
      and accurate time synchronization.
    - Aurora: compatible with Postgres and MySQL, but can run three to five times faster.  
      Accomplishes this with a high performance auto-scaling shared storage layer, so it can scale to enormous heights.
    - Table design
        - Relationships, one-to-one, one-to-many, many-to-many.  Join tables.
        - Constraints
        - Primary Keys, secondary indexes
        - Data types
    - **Normalization:** splitting records into separate tables, adding foreign key constraints as needed, to reduce data duplication.  
      There is also statistical normalization, which refers to subtracting the mean of data and dividing by the standard deviation.
        - The goal is to reduce or remove duplication in tables.  Also simplifies queries and improves data integrity.  
          This eliminates the possibility of there being discrepancies, inconsistencies, or anomalies in the data.
        - **First normal form:** column values are atomic and cannot be divided.  Rows are unique.
          For example, address should not be a column because it has sub-parts.
        - **Second normal form:** Removes redundancy in tables by shifting common subsets of data into separate tables for joining.
          For example, if the value of one or a few columns are easily determined by the value of another, and it is not a derived field, 
          that implies it could be moved to its own table.  For example, a table with employees that contains a company address 
          field could instead leverage a separate companies table and join them.
        - **Third normal form:** removes transitive dependencies, where non-key attributes depend upon non-key attributes.
          Implies adding derived fields.

#### CAP Theorem

- **Consistent:** Reads after write return the data written immediately, and consistently.
- **Availability:** The database is still usable if a subset of its servers crash.
- **Partition Tolerant:** The data can be partitioned among different servers.
- Choose two of the above.  Exception for Spanner, which leverages a high speed network connection 
  and time synchronization to achieve all three.

#### ACID

Prioritizes consistency over availability.

- **Atomic:** A change happens all or none.  Each transaction is treated as a single unit of work, either all of its changes are made, 
  or none of them are made.
- **Consistent:** Once a transaction has been made, reading the data back will give consistent results.  
  The transaction can only shift the database from one consistent state to another.  It cannot leave the database in an inconsistent state.  
  Table constraints, such as foreign keys, uniqueness, etc., must still be met after transactions.
- **Isolated:** A transaction will not affect other transactions.  Changes made by one uncommitted transaction are not visible to other connections.
- **Durable:** If there is a system failure, any data that was stored by a completed transaction is quickly recoverable.  
  Even if the database crashes and recovers, it will not have lost any data.  This is usually done with a WAL, write ahead log.
  Completed transactions remain completed even after system failure.  Usually involves writing them to non-volatile memory.
- **Transaction:** all changes are made, or no changes are made.

### Postgres

- **ORDBMS:** object relational database management system.
- ACID transactions, foreign key constraints, triggers, stored procedures, SQL, nested sub-queries, views. 
  Joins, aggregate functions, schemas, custom types.
- Can write stored procedures in Python and some other languages, but not in SQL.
- Better support for complex queries than MySQL.
- Better support for data integrity constraints than MySQL
- More extensible than MySQL.
- Supports roles
- Uses **MVCC:** multi version concurrency control.  In MVCC, the database may maintain multiple copies or versions 
  of rows or records.  This enables concurrent transactions to have isolation, to not view changes made by the 
  others, without using locks.  When a transaction modifies a row, that creates a new version of it, only visible 
  within the transaction.  Often times a timestamp is used to determine visibility of the rows.

### MySQL

- Considered easier to use, and more user-friendly, than postgres.  Preferred for simpler and read-heavy workloads over Postgres.
- Considered faster than Postgres, probably due to relaxed consistency guarantees.
- Transaction support is optional, it requires InnoDB.
- Queries can refer to different database tables.  I don’t think PostGres allows this.
- Can explain queries.
- MyISAM storage engine is older, does not support transactions, nor MVCC, no foreign key support, and locks by table.  
  It does not support table partitioning.
- InnoDB is the default engine by version 8.4.  Row level locking.  ACID transactions.  MVCC.  Caches frequently used records in memory.

### AWS RDS

- Managed service, Amazon performs: patching, backups, provisioning, configuration, recovery, failure detection, and repair.
- Can choose from: Postgres, MySQL, MariaDB, Oracle, SQL Server, and DB2.  Aurora can be used with just MySQL and Postgres.
- Can have up to two read-only standbys.
- Enables high availability by hosting instances in multiple availability zones.
- It sets up monitoring for you out of the box, many metrics are automatically sent to CloudWatch for CPU, memory, disk usage, 
  IO, and connections.  There is no added charge.
- It enables easier integration with various AWS services: send notifications or alerts with SNS, 
  or copy data into Redshift for analytics or machine learning.
- notebook:
    - db instance can run multiple database types
    - database parameter and option groups to configure them
    - RDS MySQL: multiple AZ possible, HA, read replicas (scalable)
    - same for postgres
    - Aurora: components more service oriented, much faster, fully managed
        - works using a cluster volume that spans AZs
        - data is auto-replicated across AZs using the volume
        - primary instance supports R&W
        - 15 replicas max, read-only
    - RDS supports automated backups.
    - RDS backups are done daily during maintenance windows, can set their retention period
    - snapshots of entire instance
    - manual snapshots don't auto-delete
    - latency can occur during backups.
    - vertical scaling only.  scaling done manually, short disruption
    - can recover with transaction logs too, to get up to last five minutes
    - RDS writes are synchronously replicated across AZs
    - RDS failover is automatic (CName is updated)
    - auto-failover takes one to two minutes
    - standby databases are not read replicas, they are for DR only
    - for performance use read replicas or elasticcache
    - read replicas can be in other regions
    - RDS can only be launched into subnets that explicitly allow it, called db subnet groups
    - can encrypt at rest and in transit.
    - the database SGs start out denying all traffic.
    - you cannot extract one table from a snapshot, the entire snapshot must be restored.
    - replication to standby is synchronous, while read replicas are asynchronous.
    - you can manually promote a read replica to master but you could lose data.
    - with aurora there is no chance of data loss.  read replicas automatically become master in a failover.  there is no need for a hot standby, the replicas take both roles.
    - conventional rds allows a maximum of five read replicas.  failover means 1-2 minutes of no write capability, and hence the database is offline.
    - microsoft SQL Server only supports "bring your own license" models, you cannot purchase a license over AWS.

#### Aurora

- Fully managed.  Is considered part of RDS.  There is a serverless option, you must choose a specific instance class.
- Claims to be both faster and less expensive.
- Uses a high performance distributed storage subsystem, which can automatically scale up/down for more or less data.  
  Storage of both logs and data is separated from database instances.  Data is automatically replicated to multiple availability zones.
- Automates database clustering and replication.
- There can only be one primary database instance that is capable of writing data.  There can be up to 15 (read-only) replicas.  
  If the primary instance fails, Aurora will automatically failover to a Replica, promoting it to be primary.
- Even if you only have a primary instance and no replicas, the underlying storage is still replicated to multiple availability zones.
- Aurora automatically and continuously backs up data to S3.
- Instances perform sql  parsing, query planning, transaction management, and caching.
- Uses a cluster volume, which is a virtual storage drive that replicates data to three availability zones within one region.  
  Drives use SSDs.
- Database Instances can come and go without any data being lost or migrated.
- Enables Blue/Green deployments:
    - Creates a synchronized clone of the database called the staging instance.
    - In the staging instance (clone), you can make major or minor upgrades to the database version, or change its configuration.
    - The staging instance (clone) can then be promoted to be the production instance.
    - There is a period of downtime, but it’s usually brief, under a minute.
    - Some database engines do not support BG deployments.
- For simpler use cases, Aurora can be more expensive than plain RDS.  If you don’t need HA, replication, lots of IO, etc., 
  then it may cost less to avoid Aurora.

### NoSQL

These do not support foreign key constraints.  Usually they do not support transactions, but there are exceptions.  
Frequently they do not support SQL queries.  They tend to support partitioning, replication, and scaling better than relational databases.  
The main advantage of NoSQL is that you can leverage multiple nodes in a cluster to run the service.  Often times they can scale up/down 
without any downtime.  They often support unstructured or semi-structured data and don’t necessarily need a schema.

These are further separated into these sub-types:

- **Document Stores:** flexible schema, stored as json, bson, xml, etc. Query support.
- Cassandra
    - Distributed record store
    - Eventually consistent
- **MongoDB:** Distributed document store.
- **DynamoDB:** Amazon’s document store.  Serverless.  highly scalable.  Somewhat expensive.
- **Firestore:** Google’s document store.
- Key/Value stores:
    - HBase:
        - Runs on HDFS
        - Partitioned tables
        - Sparse record store
    - **BigTable:** Like HBase, but by Google, scales better, doesn’t need HDFS.
    - **Accumulo:** Like Accumulo, but adds a visibility column for security.
- Graph databases:
    - **Neo4j:** single machine, older and more popular, doesn't scale as well.
    - **Nebula:** is distributed
- **Warehouses, Columnar storage:** ideal for analytics or batch processing.  Often has schemas.
    - BigQuery
    - Redshift
    - Snowflake
    - DynamoDB:
        - Serverless, NoSQL, managed, highly scalable and available.  Zero Downtime maintenance.
        - Supports very large tables.
        - Key/Value or document models.
- “DeNormalize” data to avoid having joins or round trips.  Doesn’t support join.
- Strong read consistency, ACID transactions across multiple tables within one region and account.
- Supports global tables, they can be active in multiple regions.  So if your application fails-over to a different region, 
  there is no database delay.
- Can stream changes to data to Kinesis or DynamoDB Streams.
- Secondary indexes.
- Tables do not have schemas.  Every record can have different attributes, except for the primary key.
- Primary keys can have a partition key and optionally a sort key.  If there is no sort key, the partition key must be unique.
- Attributes can be nested.
- Tables are partitioned by a partition key, which is the first primary key attribute.
- The extra attributes in a primary key are sort keys.  Dynamo stores records with the same partition together, 
  in sorted order by the sort keys. 
- Global Secondary Indexes: different partition key and sort key
- Local Secondary Indexes: same partition key, different sort key.

## BigTable

- Sparsely populated, hugely scalable table.  Rows with indexed keys.
- Tables are sorted by key.
- Columns are grouped into column families.
- Cells are versioned with timestamps.  You can see a history of changes.
- Columns with no value for a row do not take up any space.
- Nodes are also known as tablet servers.  Each node handles a subset of requests.  You can add more nodes at any time and 
  it increases the supported request rates and throughput..  
- Storage layer is called Colossus, Google’s file store, it holds SSTable files, which I’m guessing are used in a B-Tree.  
  SSTable files are immutable and persistent.
- Tables are split into consecutive ordered rows called tablets.  Tablets are assigned to one node.
- All keys and values are simply bytes.
- Uses a write-ahead log on Colossus for durability.
- Nodes don’t hold the data, only Colossus does.  This is how scaling is so easy, because when new nodes are added and removed, 
  it just changes assignments, but no data is migrated.  Same goes for node failures, only small amounts of metadata needs to be migrated.
- Data replication is possible by having multiple clusters.
- It automatically rebalances tablets, splitting and grouping them based on traffic and size, and re-assigning them to nodes 
  to balance their traffic.
- To get optimal write performance, you should choose keys that tend to not be consecutive. 
  Timestamps or incrementing counters should be avoided as - the start of a key.  Choose keys that tend to be written across the 
  whole key space.  
  However, you may want to choose a key that groups related data together.
- Compaction occurs periodically to remove deleted rows by re-writing SSTable files.
- On disk, it writes a line for the row key, column family, column qualifier, version/timestamp, and then cell value.
  Having shorter column families and qualifiers can help, and it’s also a good idea to use column qualifiers as data.
- Deletions are markers that a row was deleted.
- If you use multiple clusters, they are eventually consistent.

## Redshift

- you can manually add compute notes
- define your own distribution strategy, and sort keys
- storage compute: resize ops mean it creates a new cluster and migrates data
- during resize it is read only
- existing columns cannot be modified.
- auto-selects a compression method per column via sampling
- three distribution styles
- keys: stores matching values close together to improve join speeds.  keys are from one column
- even distribution
- all: each node has a full copy of the table (reference tables)
- sort keys: improves range predicated query performance
- sort keys are compound or interleaved
- use vacuum command after bulk uploads, to reorganize, and reclaim space from deletes.
- "analyze" updates table stats.
- WLM: workload management: prioritizes queries for users.
- uses a four tier key based architecture for encryption.

## HBase

- "hbase shell" starts the shell
- use single quotes around table names, rows, etc.
- no databases yet.
- must have at least one column family (cf).
- no concept of types, no data aggregation from shell
- must explicitly create alternative indexes.
- five primitive commands: get, put, delete, scan, increment
- CRUD, create and update are both from the put command
- no shell method to query (mongo does have that)
- mongo lets you store vaues of any type, HBase and Accuulo force you to convert them to byte arrays
- constructor arg for put is row key
- in hbase, rows are not overwritten all together with an update, just what is changed.
- mongo stores documents, hbase has rows
- one memstore per column family
- hbase data is stored in memory in a MemStore, stored in HDFS as an HFile.
- An HFile has data for only one CF, not multiple
- when a memstore is flushed it creates a new hfile, does not write to an existing one.
- put operations go to WAL first, then memstore,
- WAL is aka HLog.
- one memstore per cf
- single wal for an hbase server, shared by all tables
- each cf also has a block cache, which is a LRU cache of data in the HFiles, so it can avoid doing a disk read.
- HFiles are indexed by block, they are split up into blocks, a block is the minimum size read.
- Block is the unit of data read into memory by HBase in one pass.
- Block is smallest indexed unit of data
- 64kb is the default block size
    - increase it for frequent sequential scans,
    - decrease it for frequent random lookups.
    - decreasing it means the index will aconsume more disk.
- to do a read, hbase must check the memstore, block cache, and all hfiles that might have relevant info.
- writing memstore to hfile is merly called a flush.
- minor compactions merge multiple hfiles into one, but not all.
- major compactions merge all hfiles for a region and cf into one hfile.
- major compactions are the only way to clean up deleted records
- current time in millis is the default versino number when a record is created.
- old versions get deleted in major compactions
- delete column vs. delete columns differ by whether they delete one version or all versions of a cell./
- "column" by itslef means column qualifier (CQ)
- table names and cf names are strings
- row keys and values are byte[] and CQ version numbers are long.
- can't enforce constraints
- transactions over multiple rows?
- edis to a single row are atomic.
- can put data into some row, embedded, if you want it to be "transactional"
- timestamp/version is sorted in descending order.
- hfiles -> line per cell+version
- `rowkey, cf, cq, version value`
- funny that it includes cf
- doesn't include table name
- data for one row can be in mutiple hfiles
- hbase admin can be used to manipulate tables from java
- mongo will auto-generate row keys (_id), hbase will not.
- you should increase scanner cache size if it can help performance.  The default is 1, meaning it makes 1 rpc call per row of scan, it can be much more efficient if it batches (caching) or pass an int to the "next" function.
- CheckAndPut -> checks if a cell has a certain value, if it does then execute the given input, return true if it did the put, false otherwise.
- does not throw an exception if the check fails.
- this can be used to only set new values, by passing null as the expected value.
- Notice your check doesn't have to be for the same row as your put.
- CheckAnd* are atomic operations
- Root- never splits, .META does
- ROOT- points to a client to a region in the .META table
- .META tells the client what region server has the region the client is seeking.
- the -ROOT- table is found using zookeeper.
- clients cache the information about -ROOT- and .META.
- you should disable speculative execution if mapreduce jobs interact with hbase
- don't run m/r on same cluster as production, hbase should be low latency and m/r will slow it down, and put a burden on it.
- can disable jobtracker and tasktracker daemons on the hbase cluster if not using m/r
- you can think of cqs in hbase as being similar to mongo field names.
- changing a cf means the table must be taken offline
- one access pattern should map to one cf
- using short names for cfs, cqs, keys, will improve performance of IO b/c hfiles store them in each line.
- Tall tables have better performance than wide ones, but sacrifice the ability to atomically update.
- de-normalizing, having redundant data in a table, means you can skip an extra scan, but now the two tables may mismatch.
- relational databases encourage normalization, nosql data stores encourage some de-normalization.
- normalization optimizes for writes, de-normalization optimizes for reads.
- can adjust block size in a cf to be larger/smaller, usualy with smaller rows, or with frequent random access, you may want to reduce the block size. 
- increase it to save on RAM.
- can disable block cache, useful if a cf is only accessesd by sequential scans, is not accessed often, or it's frequently having large sequential scans done.
- disabling it can save you ram and improve performanne in that situation.
- can elect to aggressively cache a certain cf, meaning hbase prioritizes keeping it cached.
- bloom filters are not enabled by default, can be enabled on rows or rowcol,  rowcol will index row+cq values combined.
- bloom filters provide a negative test for each block, so a scan can quickly determine if it's safe to skip a block.
- you can set a ttl on a cf, major compactions would use the timestamp (version #) to determine if it can delete a row.  ttl is infinite by default.
- can enable compression, using a codec, but hbase only compresses the hfile, not memstore, or blockcache.  
- when the data is sent over the network, it is not compressed.
- altering a table setting like these usually won't take full effect until a major compaction is performed. 
- for example, altering a table ot use compression will not immediately update all the table's hfiles, but they will be after the next major compaction.
- compression is not enabled by default, but usually you do want to enable it.
- if your data is rarely redundant, and won't get smaller by compression, you could leave this disabled.
- enabling compression does require more CPU, so if your cluster i already cpu bound you may want to leave this disabled.
- if you want to save on ram or network IO, you may want to have the client compress data.
- default # of cell versions is 3, often you only want 1, or unlimited.  canc change per CF
- MIN_VERSIONS is used in conjunction w/ttl.
- hbase bulk loading uses a map/reducejob to produce hfiles, and then puts the hfiles directly into hbase/HDFS.  The files generated are called storefiles.  Directly loads the StoreFiles.
- The WAL is not written to during a bulk load.
- HFile and StoreFile are synonymous, you generate HBase data files (StoreFiles) using HFileOutputFormat.
- each region server has its own WAL.  edits/puts go to wal before memstore.
- ICV: increment column values.

## Data Warehouse

- Serverless, managed
- Can use SQL or Python to run analytics.
- Columnar storage format, optimized for analytical queries.
- Highly available replicated data.
- Supports transactions.
- Data is usually loaded by streaming or batch jobs.  Can ingest other common data formats like orc, avro, parquet, csv, json.
- Can query data that is not in BigQuery, like from Spanner or cloud storage.

## DynamoDB

- Serverless, NoSQL, managed, highly scalable and available.  Zero Downtime maintenance.
- Supports very large tables.
- Key/Value or document models.
- “DeNormalize” data to avoid having joins or round trips.  Doesn’t support join.
- Strong read consistency, ACID transactions across multiple tables within one region and account.
- Supports global tables, they can be active in multiple regions.  So if your application fails-over to a different region, 
  there is no database delay.
- Can stream changes to data to Kinesis or DynamoDB Streams.
- Secondary indexes.
- Tables do not have schemas.  Every record can have different attributes, except for the primary key.
- Primary keys can have a partition key and optionally a sort key.  If there is no sort key, the partition key must be unique.
- Attributes can be nested.
- Tables are partitioned by a partition key, which is the first primary key attribute.
- The extra attributes in a primary key are sort keys.  Dynamo stores records with the same partition together,
  in sorted order by the sort keys. 
- Global Secondary Indexes: different partition key and sort key
- Local Secondary Indexes: same partition key, different sort key.
- notebook:
    - configure read/write capacity, auto-scales- always uses SSDs
    - replicated across AZs (HA)
    - just need to define table key name
    - 400 kb max item size
    - columns don't need to be defined
    - eventualy consistent by default
    - supports scalars, sets (string, number, binary), and documents (list, map, nesting).
    - dynamo builds unordered hash indexes by primary key
    - primary key must be unique, and consists of partition key, and an optional sort key.  If sort key is included, the 
      partition key does not need to be unique.
    - each request can specify a consistency level, but higher numbers consume more read capacity
    - global secondary indexes: any time, any key, any sort, unlimited
    - local secondary: same key, different sort, at creation only, one per table.
    - secondary indexes consume write capacity
    - update item supports atomic counters.
    - can batch write 25 items.
    - query: searches all values in a partition key
    - scan: searches all of the table
    - have to page through if over 1MB
    - 10GB per partition
    - once partitions split, they never merge
    - choose a partition key that is written/read uniformly
    - dynao supports FGAC, down to specific attributes
    - dynamo db streams: keeps a list of table modifications over past 24 hours, in time order. 
        - associated with table, can be enabled or disabled.
        - streams are sharded.

## Cassandra

- nosql, huge data, open source
- key-value pairs
- replicated data
- masterless, clustered
- no constant master or slaves.
- can config number of nodes
- sync data to.  
- By Facebook
- Apache licensed
- "Rings": nodes are connected in a ring
- Gossip: node tells another.  #2 in background
- if a node goes down, commands go to the second.
  You can control how many nodes that data gets replicated to.
- you can control sharding of data
- each node can receive a request, and it becomes the coordinator for that request.
- Writes are very fast.
- can have unstructured data
- no relations, but has collections.
- no joins.
- each row can have a different set of columns.
- column family is like a table.
- writes to a WAL, then memtable, to SStable on disk, and another node.

# Distributed Configuration Key Value Stores

Etcd, zookeeper

# Time series

InfluxDB, Prometheus

## Caching

Tend to be more focused on low latency retrieval of individual values.  Oftentimes involves storing data in memory instead of disk. 
May not hold all of the data.  Many databases perform their own caching on frequently used data.

- **Redis:** In memory, can expire records.  Advanced object types.  Very fast.
- MemCached: Key/Value
- **L1 Cache:** close to CPU, small, faster, more costly.
- **L2 cache:** farther from CPU, larger but slower.
- **Pre-caching:** Writing to the cache before data has been requested.

### Caching Strategies

- **Cache aside:** aka lazy loading.  Data is first retrieved from the cache, if it’s not there, then it is retrieved from
  the data storage layer, written to the cache, then returned.  There is potential for data to be stale, if records are mutable.  
  On the other hand, if records are immutable and read frequently, this can deliver great performance.
- **Read-Through:** The application always retrieves data from the cache, but the cache is responsible for fetching any data 
  that it does not have.  Ideal for read-heavy applications.  Simplifies writing the application.  May return stale results 
  or require complex logic to keep the data consistent.  Keeping the cache in sync with the database can be complicated.  
  Maybe change streams could help.  Also great for immutable data, or rarely updated data.
- **Write Through:** Data is written to the cache simultaneously with the database.  The cache is responsible for immediately 
  writing data to the database itself.  Easily keeps data consistent.  Slows down writes, but can speed up reads.
- **Write back or write behind:** Data is written to the cache first and only.  Later the cache writes it to the database.  
  There is a risk of data loss.  Permits subsequent updates to the object, like if a - draft is being developed.  
  Useful for things that have not been committed yet.
- **Write around:** app always writes to database and reads from cache, if there is a cache miss then it acts like cache aside.

### MemCached

- Distributed, in memory, key-value store and cache.  Open source.
- Very low latency and supports very high request rates and simultaneous connections.
- The key space is partitioned among servers by hashing the key.  It’s a large hash table.
- Is a LRU, least recently used cache.  Items expire after a set duration.
- Keys and values are abstract strings or byte arrays, it does not interpret them.
- Has increment/decrement commands.
- Servers do not collaborate, they are oblivious of each other.  The client decides which server to contact based on the key’s hash.
- Can say never expire a key/value with expiration 0.  Max expiration seconds is 30 days, but you can set expirations after that, 
  you just need to provide a unix timestamp date.
- Newer versions periodically scan the cache and delete expired data, older do not.  Trying to read an expired record will result in it 
  being deleted.
- There is an add command that sets a value if it does not exist, otherwise leaves it as is.  It’s used for initializing counters or locks.
- Can store items on disk, but it’s meant for special disk types that are more memory based.  Flash based storage, 
  it says using a rotational hard drive “won’t work”.  Least used records go to disk.  Also DAX filesystem mounts 
  for persistent memory are supported.  DAX is code that enables Direct Access to files in a memory-like block storage device, 
  like flash or SSD, by skipping any copying of data to or from a page cache.
- Has some support for a warm restart.
