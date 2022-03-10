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

## Chapter 3





