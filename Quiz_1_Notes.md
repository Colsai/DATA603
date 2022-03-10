# Quiz 1 Notes

## Chapter 2:
Consistency, Availability, Partitioning = Cap Theorem

## LinkedIn Diagram
LinkedIn Web Application (Many Servers) -> Middleware Distributed transport -> 
Spark Streaming (Low Volume, high velocity) and Hadoop HFS (Analytics, matching/recommending), high volume, low velocity.

File systems are methods and data structures that operating systems use. Name server with protection via access control, storage, retrieval, and naming of items.
File System Architecture- Comes with logical file system (allowing interaction), or Virtual file system, or Physical file system
Application -> Logical File System, OS, Virtual File System -> Ext3/NTFS/NFS (Physical File System)
Has Space management, file names, directory names, and metadata

Files can be unstructured, structured (direct addressing), associative addressing (key, value organization)

## Distributed File Systems
Distributed file systems manage storage across a network. They don't share the same storage but use a network protocol, 
which allows files to be accessed using the same interfaces as local files. 

### They have a few aspects about themselves: 
- Access transparency (clients can use them just like local file systems
- Concurrency (Processes are presented similarly)
- Scalability (Can deal with small to large traffic)
- Replication (Files are replicated across different nodes for the scalability)
- Fault Tolerant: Can heal during hardware failures

One example is HDFS (Hadoop distributed file system).
HDFS has a client, name node, and secondary name node.
Name node connects to data nodes, which connect to local disks. 
Hadoop was made to run on commodity hardware- low-cost.
It provides fault tolerance, with automated detection and recovery from failures.
It also has high throughput access, and was made to support large files, have high bandwidth, and high throughput. However, it is not a good choice for low latency data access (
streaming)

## Advantages of Hadoop
HDFS uses a simple coherency model (write once, read many times). Once a file is created, it does not need to be changed. This is good for mapreduce.
Moving Computation, not the data, where compute resources are near the resource itself. This alsmo minimizes network traffic, and increases throughput.
Cross Platform Portability- This increases adaptation of HDFS systems

## Architecture:
Hadoop uses datanodes, which are workers of the file system that manage storage. Files are split into multiple blocks and stored on data nodes.
These datanodes perform read and write requests for the file system's clients. They perform block creation, deletion, and replication upon instruction from name node. 

## HDFS - Namespace (In computing, a namespace is a set of signs (names) that are used to identify and refer to objects of various kinds.):
Namespace is the file organization, in which tree like directories can be created, and other files and directories can be stored in a directory. You can also set quotas on
directories.

## HDFS Permissions
HDFS uses a POSIX model for file and directory access, where files and directories have an owner and group. There are separate permissions for the owner, group users and
other users. POSIX (https://en.wikipedia.org/wiki/POSIX) is a Portable Operating System Interface (POSIX) made with standards for interoperability.

## HDFS Data Replication
Files are stored on sequences of blocks, where blocks are all the same size (128mbs default), these blocks are replicated for fault tolerance. Blocks and replication factor
are configurable. These files are write-once and have only one writer at a time. Decisions regarding replication of blocks are made by the name node.
At startup, datanodes determine the rack ID and send it to the namenode for registration. The rack ID is determined using modules providing rack ID detection.
One replica is on one node in the local rack, one is on a different node in the local rack, and another is on a different node in a different rack. The goal is to reduce
latency caused by the network bandwith between machines, and still have high availability to survive failure. 

## HDFS Data Storage
Data in each datanode's local file system, but the datanode has no knowledge about these files. Heuristics are used for optimization. Datanode startup scan through and 
generate a list of any HDFS data blocks corresponding to local files and sends list to name node. 

Read: HDFS Client asks namenode where block locations are, and namenode responds. HDFS client then reads from datanodes with data.
Write: HDFS Client tells name node to create a file, then writes to a datanode, which sends back an acknowledgement, and HDFS client sends notification of commit to Namenode.
Datanodes work to replicate data.

# HDFS IS GREAT BUT COMPLEX FOR MAINTENANCE. THERE ARE ALTERNATIVES.
### HBASE: 
Using distributed key/value store, column oriented database built on top of HDFS that promises consistency.
It is used for real-time read/write and random access to large datasets.
Sparse, distributed, persistent, multidimensional sorted map indexed by row key, column key and time stamp.
Not relational, allows structured and semi structured data naturally. Its modeled after Google BigTable.

### Apache Accumulo
Distributed key/value store created by the NSA and based on bigTable.

#### Apache Cassandra
Highly available, distributed, partitioned row store NoSQL database that follows Dynamo DB design principles. Distributed database hat is a collection
of nodes working together to serve the dataset. It has a partitioned row store, and is decentralized, with every node being identical. 

<hr/>

## Chapter 3 MapReduce
MapReduce is a software framework for vast amounts of data in-parallel on large clusters. It is a computing model that decomposes large data manipulation jobs into tasks that can be execulted in parallel across a cluster of servers, where tasks are then joined together for the final results. 
Mapreduce can process large datasets and generate answers, and be run on commodity hardware. It processes a lot of data on many machines, and uses master-worker model.
It helps in parallel computing.

The run time in hadoop takes care of the details of partitioning (splitting) input data, scheduling the program's execution across nodes, managing inter-node communications, and handling of node failures, so only code is really left. 

MapReduce takes two processes, MAP (accepting key/value pair and emitting intermediate key/value pair) and Reduce, where it accepts those intermediate key/value pairs and outputs key/value pair. Processing is broken into a mapping phase and then reduce phase. Key value pair is passed as the input, and returns a key-value pair as the output. The types of keys are chosen by the programmer.

## Map Phase: 
Done by mappers, which take unsorted key/value pairs. Mappers then return key value pairs. Input and output keys and values can be different. 
Reduce phase is done by reducers, which take key/value_list sorted by keys. This is done by the framework (Hadoop), and contains all values with the same key produced by mappers. This will return zero, one, or many key/value pairs for each input, and convert value_list to a value (sum, average, etc.)
There is more to MapReduce than map and reduce,

- Input data/split into smaller splits/mapper task/combiner, which reduces amount of data transferred/outputs of mapper are sorted and shuffled/reducer task/output file.
Hadoop sorts key-value pairs and shuffles all pairs with same key to same reducer

- In this process, Hadoop makes sure MapReduce runs successfully, as well as breaks down jobs for map and reduce tasks, schedules running of tasks, decides where to send tasks monitors each task for success/failure, and restarts failed tasks.

- MapReduce jobs are units of work to be performed, where a job consists of input data, MapReduce program and configuration information,. Hadoop divides jobs into a number of tasks (map and reduce tasks), and tasks are scheduled by YARN and rune on nodes. Failed tasks will automatically restart. 

## MapReduce Execution overview
MapReduce splits input files into M pieces of 16-64mb. Master picks idle workers and assigns each a map or reduce task. Workers parse key/value pairs from input and pass to user defined map function. The intermediate key/value pairs are buffered in memory. These pairs are written to local disk, and partitioned into R regions with paritioning function. This data is sorted as intermediate data by intermediate keys, then grouped together. The output of the reduce function is appended to a final output file, and mape reduce calls in the user program and returns back to user code. 

## YARN
Yet another resource navigator, it splits functionalities into separate daemons, for resource management and job scheduling/monitoring. 
There is a ResourceManager (RM): One per cluster that manages the use of resources, a nodemanager (one per node) that monitors resource usage and reports back to the resource manager/scheduler, last, a per-application ApplicationMaster (AM): a framework specific library tasked with negotiating resources from the RM and working with the NodeManager to execute and monitor tasks. 

### ResourceManager
- Has a scheduler, which allocates resources (performs no monitoring nor guarantee), subject to capacity
- Has an applications manager, which accepts job-submissions, and negotiates the first container for executing the application specific ApplicationMaster, provides servies for restarting on failure. 
Clients submit job to resource manager. Application master sends resource request to resource manager, and containers within nodes send their status to application master for monitoring. 

Originally, MapReduce had a jobtracker, tasktracker, and slot. Now, YARN has the RM, AM, and timeline server, with node manager and container.

Client programs submit job to job tracker.
In the map phase, input files are split, and sent to task tracker.

For word count, this works as input, splitting, mapping, shuffling, reducing, and final result.
input file is split into items, which are mapped to a count, then mapped again if matching (with corresponding count), which is reduced to a value, and exported into a list.

## Other Examples
- Cross Reference Indexing: Map function parses documents and emit sequences of words. Reduce functions accept all pair sfor any word, and sort document ID and emit a word, list, and the set of all output pairs forms the answer.
- Distributed Grep: (Globally search for Regex and printout), searches a file for a particular pattern of characters, and displays all lines that contain that pattern. The pattern is searched in the file as a regular expression. 
- Map function emits word, line_numbers, and reduce copies the supplied data to output.
- Count of URL Access: Map processes logos of web page requests and outputs <URL, 1>, and reduce adds all values and emits a pair <URL, Total Count>
- Reverse Web-Link Graph: Map outputs <target, source> pairs for each link in a target URL in a page named source, and reduce concatenates the list of all source URLs associated with a given target URL and emits the pair: <target, list(source)>

## Refinements
- Task granulatirty (Splitting), where mapping the task into a large set of smaller tasks allows us to benefit by minimizing time for fault recovery, load balancing, local execution for debugging/testing, and compression of intermediate data. 
- Input Splits: Inputs are divided into fixed-sized pieces. Map tasks are created for each split, and by processing splits, it is faster than processing the whole output. These splits are processed in parallel.
- Size can matter for splits, smaller size can offer better load balance, and faster machines can pick up more splits, but there is also overhead in managing splits and there is time required to create map tasks. The size of HDFS blocks is a good default. 

## Fault Tolerance
- Worker Failure: Workers are periodically pinged to check status. If processors fail, the tasks of that worker are reassigned.
- Master Failure: Master writes periodic checkpoints. Another master can be started from the last checkpoint, but if the master dies, the job is aborted.
- If worker fails, master reassigns a new worker. The max number of failures is configurable, and it applies to both map and reduce workers.
- If map workers fail at any time before reduce finishes, the reduce task has to be rerun entirely.

## MapReduce Characteristics
- Master/Worker Moedl is used, Master is the job tracker, and the worker is the task tracker. Reduce can't begin until map is complete. Master communicates locations of intermediate files, and tasks are scheduled on the location of the data. 

## MapReduce and Python
- Some MR packages- MrJob (allows multi-step mapreduce jobs in python, testable on local machine and runnable on hadoop cluster, EMR, dataproc, spark).
  - Creates a class that inherits from MRJob, it contains methods that define steps of map/reduce. Mapper method and reducer method, which takes a key and a value as parameters, or key and iterator of values.
- Pydoop

# MapReduce is the idea of divide and conquer!

## Cloud Computing
Method of computing using rented resources (compute power, storage, networking, and analytics), only paying for what is used. Cloud providers are providing physical hardware and infrastructure. 

### Benefits:
- Cloud computing is cost-effective because there is no infrastructure cost, nor need to maintain servers. it is scalable, and allows for scaling out as well as horizontal scaling, and it is elastic, automatically adding or removing resources based on the workload. 
- Reliable, with data backup, recovery and replication options
- Secure: Cloud provider can provide a lot of the security ask
- IAAS (Infrastructure as a Service, rent servers), Platform as a service (Azure), Software as a Service (Dropbox) or Mailchimp.
- There are many cloud analytics services, from data warehouses (azure synapse), advanced analytics (databricks), data integration service (azure data factory), Machine learning (Azure ML), and storage (S3)

## Data lakes
Data Lakes are large repositories of data, usually as object blobs. It can include structured, unstrucuted, or binary data, and either on premises or in the cloud. If data has issues in running, it can deteriorate into a data swamp.

### Data lakes are good at huge volumes of data, where data can be raw or refined, allows for some data transformations, requires metadata driven approach, and good for advanced analytics and discovery.
### Data warehouses are good for cheaper costs, cleansed data that efficiently uses CPU, transforming data once, and good for historical analysis or repeatable reporting cases. 

# Chapter 4
## Cross-Cutting Principles (principles across multiple types of data)
- Reduce I/O: Reduce the input and output requirements, which is the communication between the information processing system and outside world
- Data Serialization (converting via serialization into bytes) and compression (making data smaller by format) is very important
- Work with Key Value Pairs

## CAP Theorem: Consistent, Available, and Partition Tolerance
- Big data systems have eventual consistency, but not strong consistency
- Consistency can be tuned or adjusted to set a level for the system

## More considerations 
- Decouple Storage and Compute: Important to split storage and compute, which has benefits with a big-data system now, as data is often in huge repositories.
- Column vs row data storage, or avro vs parquet, can be important for time.
- Treatment of sparse data is also important
- Sharding, the breaking up of data into logical shards, such as different rows, columns, etc.
- I/O Bound: Data requested is slower than data consumed (more time spent requesting than processing)
- CPU Bound: Data requested is FASTER than data consumed (processor is bottleneck)
- Memory Bound: Time to complete computational problems is decided by memory.

# Chapter 5: Apache Spark
Spark is a unified engine for large-scale data processing. It incorporates mutliple libraries for machine learning, SQL, structured streaming, and GraphX for graph processing. 
It was made to be fast; uses commodity servers with multicore CPUs, large memories, efficient with mutlitheading and parallel processing. Computations are a DAG (Directed acyclic graph), with a DAG scheduler and query optimizer that constructs an efficient computational graph. 
- The graph is decomposed into tasks executed in parallel. Tungsten, the execution engine Spark uses, uses whole-stage coed generation to generate compact code for execution. 
- Spark is easy to use, with a fundamental abstraction of simple logical data structure (called RDD), and set of transformations and actions as operations (simple programming model for building big data applications in other languages).
- It is modular, with many supported languages and core components.
- It decouples compute and storage (unlike hadoop), and uses connectors to read/write.

## Spark SQL 
Integrates relational processing/tabular data abstraction with Spark API. This module for structured data provides spark with more information about the structure of the data, as well as optimizing. It can interact using SQL and dataset API

## Spark MLib
- Spark has a machine learning library for algorithms, featurization, pipelines, persistence, and utilities.
- spark.mllib package uses rdd based apis.
- spark.ml package uses dataframe based api with a more friendly api with tungsten/catalyst optimiziation

## Structured Streaming
-Scalable and fault-tolerant stream processing engine, it is based in scala, java, python, or r.
- It supports streaming aggregations, event-time windows, stream-to-batch joins

## GraphX
- Library for manipulating graphs (such as social network, or routes), it performs graph parallel computaions and provides standard graph algorithms for analysis, connections, and traversals. 

# Format

Client app/Spark Driver/SparkSession interact with cluster manager, which interacts with spark executor

-- Slide 13

