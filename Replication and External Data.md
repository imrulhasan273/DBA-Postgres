# **Replication and External Data**

---

# **Index**

---

1. Replication Overview [Query](#Query-1)
    1. Replication Jargon 
    2. Evolution of PostgreSQLReplication 
    3. Third-Party Replication Options 
2. Setting Up Replication [Query](#Query-2)
    1. Configuring the Master 
    2. Configuring the Slaves
    3. Initiating the Replication Process 
3. Foreign Data Wrappers [Query](#Query-3)
    1. Querying Flat Files 
    2. Querying a Flat File as Jagged Arrays 
    3. Querying Other PostgreSQLServers 
    4. Querying Other Tabular Formats with ogr_fdw
    5. Querying Nonconventional Data Sources

---

---


[Index](#Index)

## **Query-1**

# **Replication Overview**

---


## Replication Jargon

Before we get too carried away with replication, we had better introduce some common
lingo used in connection with it:

- Master
    - The master server is the database server sourcing the data being replicated and where all updates happen. You’re allowed only one master when using the built-in replication features of PostgreSQL. Plans are in place to support multimaster replication scenarios. Watch for it in future releases.

- Slave
    - A slave server consumes the replicated data and provides a replica of the master. More aesthetically pleasing terms such as subscriber and agent have been bandied about, but slave is still the most apropos. PostgreSQL built-in replication supports only read-only slaves at this time.

- Write-ahead log (WAL)
    - WAL is the log that keeps track of all transactions, often referred to as the transaction log in other database products. To stage replication, PostgreSQL simply makes the logs available to the slaves. Once slaves have pulled the logs, they just need to execute the transactions therein

- Synchronous
    - A transaction on the master will not be considered complete until at least one slave is updated. If you have multiple synchronous slaves, they do not all need to respond for success

- Asynchronous
    - A transaction on the master will commit even if slaves haven’t been updated. This is useful in the case of distant servers where you don’t want transactions to wait because of network latency, but the downside is that your dataset on the slave might lag behind, and the slave might miss some transactions in the event of transmission failure.
- Streaming
    - The streaming replication model was introduced in PostgreSQL 9.0. Unlike prior versions, it does not require direct file access between master and slaves. Instead, it relies on the PostgreSQL connection protocol to transmit the WALs.

- Cascading replication
    - Starting with version 9.2, slaves can receive logs from nearby slaves instead of di‐ rectly from the master. This allows a slave to also behave like a master for replication purposes. The slave remains read-only. When a slave acts as both a receiver and a sender, it is called a cascading standby

- Remastering
    - Remastering is the process whereby you promote a slave to be the master. Up to and including version 9.2, this was a process that required using WAL file archiving instead of streaming replication. It also required all slaves to be recloned. Version 9.3 introduced streaming-only remastering, which means remastering no longer needs access to a WAL archive; it can be done via streaming, and slaves no longer need to be recloned. As of version 9.4, a restart is still required though. This may change in future releases.


    




