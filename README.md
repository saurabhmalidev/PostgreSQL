# PostgreSQL

What is PostgreSQL?

PostgreSQL uses multiple OS processes, not just one process.
When PostgreSQL starts, the Postmaster/main server process starts first.
It acts as the parent/coordinator process for the PostgreSQL server.
For each client connection, PostgreSQL generally creates a separate backend process.
That backend process executes the client's SQL queries.
PostgreSQL also has background processes like WAL writer, checkpointer, background writer, and autovacuum workers.
All these processes together form the running PostgreSQL server.
They run on the machine/VM where PostgreSQL is installed.
They use the VM's CPU and RAM, while persistent database data is stored on disk.
So: PostgreSQL server = multiple processes working together, not a single process.

**Define PostgreSQL**

* PostgreSQL is a relational database management system (RDBMS), ie., the database software/engine. 
* The database is the logical collection of data managed by PostgreSQL.
* When self-hosted on a VM, PostgreSQL's database files are stored on the VM's persistent disk, while multiple PostgreSQL processes running on the VM manage that data. 
* So a cleaner statement is: “PostgreSQL is an RDBMS that runs multiple server processes on a VM to manage databases whose persistent data is stored on disk.”

# From VM perspective:
```
VM
│
├── PostgreSQL Database Engine
│   │
│   ├── PostgreSQL server/main process
│   ├── Backend process  ← executes SQL
│   ├── Query Parser
│   ├── Query Planner/Optimizer
│   ├── Executor
│   ├── Transaction Manager
│   ├── Buffer Manager
│   └── WAL Manager
│
├── RAM
│   └── PostgreSQL shared buffers, query memory, etc.
│
└── Persistent Storage
    └── PostgreSQL database files
```

# From PostgreSQL Server Perspective/ Any DB Server:
```
PostgreSQL Server
│
├── Main/Postmaster process
│   └── Coordinates the server
│
├── Backend processes
│   ├── Client connection 1 → executes SQL
│   ├── Client connection 2 → executes SQL
│   └── Client connection 3 → executes SQL
│
└── Background processes
    ├── WAL writer
    ├── Checkpointer
    ├── Background writer
    └── Autovacuum launcher/workers
```
* There are 3 main sets of processes running on the VM, and these processes call the component code; And the combination of these processes and set of components is called the PostgreSQL Server.
* These processes and components manage the data stored on the VM's Disk.
* A combination of the Server process and compoments + Data on Disk is what we call PostgreSQL Database

# From Database Engine Perceptive :
```
PostgreSQL Database Engine
│
├── Code / Components
│   ├── Query Parser
│   ├── Query Planner / Optimizer
│   ├── Query Executor
│   ├── Transaction Management
│   ├── Buffer Management
│   ├── WAL Management
│   └── Storage Management
│
└── Runtime Processes
    ├── Postmaster / Main process
    ├── Backend processes
    └── Background processes

* The Database Engine is basically the combination of the set of code and algorithms we call components and the process running on the OS of the VM, which runs that set of code when called is what we call the Database Engine.
```


# Runtime Process :
```
Runtime Processes
│
├── Main/Postmaster process
│   └── Coordinates the server
│
├── Backend processes
│   ├── Client connection 1 → executes SQL
│   ├── Client connection 2 → executes SQL
│   └── Client connection 3 → executes SQL
│
└── Background processes
    ├── WAL writer
    ├── Checkpointer
    ├── Background writer
    └── Autovacuum launcher/workers
```
* So, there are 3 primary runtime processes, where each category has a set of processes executing a variety of tasks on the OS of the VM.

# Postmaster Process :
The Postmaster/main PostgreSQL process is the main/parent process that coordinates the server, 
while WAL writer, background writer, checkpointer, autovacuum launcher/workers, etc. are background processes. 
And backend processes are different: they are server processes created for client connections and actually execute SQL queries.



	

“The Postmaster is the main coordinating process, or the brain of the PostgreSQL server. It manages server startup and shutdown, handles incoming client connections and authentication, creates backend processes to handle client sessions, and starts/manages background processes required for database maintenance and internal operations.”


**Q. Where Does the PostgreSQL Database Engine Run?**

The database engine runs inside the PostgreSQL server processes on the machine where PostgreSQL is installed. In your VM example, the engine is therefore running on the VM, using its CPU and RAM, while the database files themselves are on the VM's persistent disk/storage.
	More precisely, there isn't a separate executable called “database engine” sitting somewhere. PostgreSQL is the database engine. Its code runs through the PostgreSQL server/backend processes.
	


#VM 
```
	VM
	│
	├── PostgreSQL Database Engine
	│   │
	│   ├── PostgreSQL server/main process
	│   ├── Backend process  ← executes SQL
	│   ├── Query Parser
	│   ├── Query Planner/Optimizer
	│   ├── Executor
	│   ├── Transaction Manager
	│   ├── Buffer Manager
	│   └── WAL Manager
	│
	├── RAM
	│   └── PostgreSQL shared buffers, query memory, etc.
	│
	└── Persistent Storage
		└── PostgreSQL database files
```	
* The PostgreSQL database engine is the complete PostgreSQL software that contains the code and components responsible for managing and processing database operations. The processes are the runtime instances that execute that engine's code.

* Management Components vs Runtime Processes
Storage Management, Query Parser, Query Planner, Executor, Buffer Manager, WAL Manager, etc., are software components/modules inside PostgreSQL's codebase. The runtime processes are the actual OS processes that execute this code.











-----------------------------
-
**Replication in PostgreSQL:**

* PostgreSQL replication can be broadly divided into physical replication and logical replication. The key difference is what is replicated: physical replication copies the database's storage-level changes, while logical replication copies logical row-level changes.

* 1. Physical Replication: The primary server sends WAL (Write-Ahead Log) records to a standby server. The standby replays those WAL records, producing essentially the same physical database state as the primary. It is mainly used for high availability, failover, and read replicas. PostgreSQL's streaming replication is the common implementation. Physical replication replicates the entire database cluster, not selected tables. \
Within physical replication, you'll commonly hear:
		* Streaming replication — WAL is continuously streamed from primary to standby.
 		* Synchronous replication — primary waits for confirmation from a standby before considering a transaction committed.
		* Asynchronous replication — primary does not wait for the standby; this is the normal/default approach.
		* Cascading replication — a standby can forward WAL to another standby.

* 2. Logical Replication: PostgreSQL decodes WAL changes into logical operations such as INSERT, UPDATE, and DELETE and sends them to subscribers. You can replicate specific tables rather than the entire database. This is useful for selective replication, data integration, migrations, and feeding another PostgreSQL database. PostgreSQL implements this using publications and subscriptions

* Physical Replication Flow:
* Logical Replication Flow
  



