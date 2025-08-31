
# Data Storage

## Concepts

- Three V's of data: volume, variety, velocity.  High volume and variaty is more suitable to NoSQL.

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

## Data Warehouse

- Serverless, managed
- Can use SQL or Python to run analytics.
- Columnar storage format, optimized for analytical queries.
- Highly available replicated data.
- Supports transactions.
- Data is usually loaded by streaming or batch jobs.  Can ingest other common data formats like orc, avro, parquet, csv, json.
- Can query data that is not in BigQuery, like from Spanner or cloud storage.

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
