# AWS Databases
RDS (relational database on AWS) allows deployment across multiple AZ (availability zones) for recovery and read replicas for performance.


Recovery: Automatically point connection string from EC2 instance to a database in an AZ. If one AZ goes down, it automatically connects to the twin database in the other AZ.


Performance: An EC2 instance writes to primary database who then writes to a replica database. If the primary DB goes down though, you lose access to DB completely. You can setup so you write to primary database 
and reads from replica database. You can have up to 5 replica databases to read from.

---

Nonrelational databases are similar in content as relational databases (have tables, rows, and key/value pairs). The columns in a table can vary and will not effect other rows in the database.

AWS uses DynamoDB

Data warehousing is used for business intelligence. Used to pull in very large and complex datasets and usually used by management to do queries on data such as current performance vs 
targets, etc.

    
Data Warehousing databases use a different type of architecture when compared to the above databases.
AWS uses Redshift.

Elasticache can deploy and scale in-memory cach in the cloud. Allows web apps to get info quicker than using databases. Uses 2 engines: Memcached and Redis.




# Setting up an RDS instance with an EC2 webserver
###### 1. Create a new RDS DB.

###### 2. Go to VPC default security group (or security group of whatever VPC you're using) and add a rule to allow whatever database management language (MySQL/aurora, etc) to communicate. Set the source to the security group being used for webserver. This will allow MySQL traffic to our default VPC security group from our webserver security group. 

###### 3. Create EC2 instance and set security group as whatever webserver security group you're using.

###### 4. When setting up database on site, the host is the database endpoint found in the RDS database Connectivity and Security.

###### 5. You can set the DNS of the site using Elastic Loadbalancer to prevent needing to use an IP address to get to site.

**Note:** To create an image of the site to allow autoscaling, go to EC2, go to `actions->create image`, then follow the instructions found in the Autoscaling notes.




# Backups, Multi-AZ, and Read Replicas
##### Automated backups: 
- Allows you to recover your database to any point in time within a "retention period". The rentention period can be between 1 and 35 days.
- Takes a full daily snapshot and will store transaction logs throughout the day.
- When you do a recovery, AWS will first choose the most recent daily backup, and then apply transaction logs relevant to that day.
    - This allows you to do a point-in-time recovery down to a second, within the retention period.
- Automated backups are enabled by default.
- They are stored in S3 and  the size of S3 bucket is the same size as the database ie 10gb RDS = 10gb S3.
- During the backup window, I/O may be suspended and you may experience higher latency.

##### Database Snapshots:
- These are done manually.
- They are stored even after you delete the original RDS instance, unlike automated backups.
        
##### Encryption:
- Supported for MySQL, Oracle, SQL server, PostgreSQL, MariaDB, and Aurora.
- Done using AWS Key Management Service (KMS) service.
- Data, automated backups, read replicas, and snapshots are all encrypted.
        
##### Multi-AZ:
- Database A synchronously replicas all changes to database B in a different AZ.
- If the AZ of DB_A goes down, the DNS endpoint for the database automatically moves over to DB_B so DB operations can resume without interrupt.
- The switch also triggers during DB maintenance.
- Supports SQL server, Oracle, MySQL, PostgreSQL, MariaDB.
        
##### Read-replica:
- EC2 instances each read from different instances of replicated DB but any writing is done to one central database.
- Creates a read only copy of your database.
- Supports MySQL, PostgreSQL, MariaDB, Oracle, Aurora, MSSQL.
- Used for scaling, not data recovery,
- Must have automatic backups turned on in order to deploy a read replica.
- You can have up to 5 read replica copies of any database.
- You can have read replicas of read replicas at the expense of latency.


# DynamoDB
NoSQL database service for applications that need consistent, single-digit millisecond latency at scale. Fully managed database and supports both document and key-value data models.

###### Basics of DynamoDB:
- Stored on SSD storage.
- It's spread across 3 geographically distinct datacenters.
- Has 2 different types of read models: eventual consistent reads and strong consistent reads.
    - Eventual Consistent Reads:  
        - Consistency across all copies of data is usually reached within a second.
        - Repeating a read after a short time should return the updated data (best read performance).
    - Strong Consistent Reads:
        - Returns a result that reflects all writes that received a successful response prior to the read.

###### DynamoDB accelerator (DAX):
- Acts as an in-memory cache.
- You can get 10x performance improvement.
- No need to manage caching logic.
- It's compatible with existing AWS API calls.

    Transactions:
        +Multiple "all-or-nothing" operations
        +financial transactions or fulfilling orders
        +up to 4MB of data

###### On-Demand Capacity:
- Pay-per-request pricing.
- Balances cost and performance.
- No minimum capacity and no charge for read/write, just store/backup.
- In the long run, you  pay more per request than with provisioned capacity.
    - If you know how long you're going to have your database up, or if you need the database for an extended period of time and anticipate a lot of API calls to the DB, provisioned capacity can save money.

###### Streams:
- Time ordered record of item level changes in a table.
- They are stored for 24 hours.

###### Global Replication:
- Data replication across regions.
- Used for global applications.
- Read/write latency is under a second.



# Redshift
Redshift is a data warehouse service in the cloud. Only available in 1 AZ currently.


###### Can be configured as such:
- Single node (160Gb).
- Multi-node
    - Leader node: manages client connections and receives queries.
    - Compute node: stores data and performs queries and computations. Can have up to 128 compute nodes.


###### Advanced Compression:
- Columnar data stores are compressed much more than row-based data stores because similar data is stored sequentially on disks.
- Automatically selects the most appropriate compression scheme based on samples of your data.
        
###### Massive Parallel Processing (MPP):
- Automatically distributes data and query load across all the nodes.
- Easy to add nodes to data warehouse and maintains fast query performance at scale.

###### Backups:
- Enabled by default with a 1 day retention period.
- Max retention period is 35 days.
- Redshift always attempts to maintain at least three copies of your data on S3.
       


# Aurora
Aurora is AWS' proprietary database. It's compatible with `MySQL` and `PostgreSQL`.

###### Basics:
- Starts at 10GB and autoscales in 10GB increments up to 64TB.
- Compute resources can scale up to 32vCPUs and 244GB of memory.
- 2 copies of your data is contained in each availability zone, with minimum of 3 availability zones for a total of 6 copies.
- 5 times faster than `MySQL` and 3 times faster than `PostgreSQL`.

###### Scaling Aurora:
- Designed to transparently handle the loss of up to two copies of data without affecting database write availability and up to three copies without affecting read availability.
- Storage is self-healing.
    - Data block and disks are continuously scanned for errors and repaired automatically.



# Elasticache
Elasticache is a webservice that makes it easy to deploy, operate, and scale an in-memory cache in the cloud. Improves performance of web applications by allowing you to retrieve information from fast, managed, in-memory caches instead of relying entirely on slower disk-based DBs.



# Database Migration Service (DMS)
Cloud service that makes it easy to migrate relational databases, data warehouses, NoSQL databases, and other types of data stores. 
Can be used to migrate to AWS cloud, between on-premises instances (through AWS Cloud setup), or between combinations of cloud and on-premises setups.
    

DMS is a server in the AWS cloud that runs replication software. You create a source and target connection to tell DMS where to extract from and load to. 
    
     


# Elastic Map Reduce (EMR)
Big data platform for processing pedabytes of data using open-source tools such as Apache Spark, Apache Hudi, Apache Hive, and Presto. 
EMR is made from clusters of EC2 instances. Each node, EC2 instance within a cluster, installs different software components on each node type, giving each node a role in a distributed application like Apache Hadoop.
    
###### Node types:
- Master node: node that manages the cluster and tracks the status of tasks and health of the cluster. Required.
- Core node: node with software components that run tasks and stores data in the Hadoop Distributed File System (HDFS) on your cluster. Required.
- Task node: node with software components that only run tasks and does not store data in HDFS. Optional.

    
Within an EMR, the log data is stored in /mnt/var/log/ on the master node. If master node is lost, so is log data. You can configure cluster to periodically archive log data to S3 at 5 min intervals.

    
