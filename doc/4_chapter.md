# 4. Technologies of Redis

## 4.1 Internal Architecture and Implementation

The open-source database Redis is implemented in C and it follows an event driven model like NodeJS ("NodeJS Landing Page", 2018).
Salavtore Sanfilippo (2017) starts the Documentation with: _"Redis is often referred as a data structures server."_ ("What is Redis").
In that Sense, Redis gives access to commands to manipulate data structures, while realizing a simple TCP Socket connection for a server-client architecture (Sanfilippo, 2017).
Unique for Redis is the in-memory way of handling data.
But it also stores data in an interval on disk.
Because of this, the database is fast, while still being persistent.  

As mentioned, Redis uses an event driven architecture.
K. Mani Chandy (2006) describes an event as _"a significant change in state"_ ("Event-Driven Applications: Costs, Benefits and Design Approaches").
In such an architecture events can happen asynchronous at any time.
Those can be system or user-generated, like a simple key-stroke.
To recognize events, the software runs an event loop, Redis has implemented its own (Sanfilippo, 2017).
If an event occurs a handler can react on it.
Redis also realizes the possibility of subscribing and publishing to events.
An event-driven architecture benefits of a low coupling of components and therefore produces a highly scalable and distributed environment (Bennage and Wasson, 2017).  

In chapter 3 this paper explained all types of data-structures of Redis.
Those are all written in C and as said before, always loaded in-memory.
Sanfilippo (2017) declares, that the data types are stressed on memory efficiency.
To be persistent, Redis generates a snapshot of the data in a specified interval, this is saved as a compact Redis-Database (RDB) file.
In case of a crash or any dysfunctionality Redis will be able to replicate the in-memory structure with this snapshot.
For commands that happen while there wasn't created a new snapshot in time, Redis realizes another persistence option called "Append only file" (AOF).
Its general purpose is to log every command that changes the data.
With this log Redis can replay every command that happened, even if there is no RDB Backup (Sanfilippo, 2012).
For applications, that have a higher requirement in persistence, Redis is able to combine those two options.  

For replication Redis server uses the same persistence options as describe before, this is done by sending the AOF file to the replicating slave instance.
High Availability will be further explained in the next chapters.  

## 4.2 Query Commands

Because key-valued databases mostly don't include a SQL-like query language to fetch data, the keys are used to reference data in commands. Over a TCP socket the Redis server is parsing and processing commands that come from a client. This sub chapter is dedicated to some important commands for interacting with a Redis Database.

Redis comes with and command line interface to type in commands. After connecting to the database, the command `INFO` can be used to get an overview of all configuration parameters. The general structure of an command is `[COMMAND NAME] [KEY| LIST OF ARGUMENTS ARGUMENT]`

As defined, a simple key-value pair is declared by using `SET [KEYNAME] [VALUE]`. If a key already exists, the value will get overwritten. There is the possibility to append an option to only set a key if it does not exist or does exist. To retrieve a value the corresponding key is used in `GET [KEYNAME]`. Integer values can be incremented or decremented by `INCR` and `DECR`. For removing a key, the `DEL` command is used. For every data type exists such commands to create and manipulate data. There is also the command `EXPIRE` to make a key-value pair exist only temporally. ("The full list of commands", 2018).

## 4.3 Features

### 4.3.1 High Availability

As this database is in-memory based, Sanfilippo (2012) warns, that Redis must lose some safety in data durability.
If a crash happens while processing a transaction, data loss may appear, if RDB and AOE should fail.
To prevent this, technologies for clustering and high availability solutions exist.  

A Redis cluster includes a key space of 16384 hash slots ("Redis Cluster Tutorial", 2018).
Each slot represents space for a key-value pair, thus making it possible to store 16384 keys.
Each master node in this cluster will handle a subset of this slot space, e.g. if there are three Nodes in a cluster every node may contain approximately 5500 hash slots.
When a key is set by a client an algorithm is used to hash it and determine to which node it will belong.
This makes scaling up easy, as the cluster software only needs to move parts of the hash slots to the new node.
Every master may be connected in a full mesh with every other node and control slave nodes.
By sending its AOF log to the slaves, it is possible to replay every action the master has processed.
Resulting in a distributed and redundant data storage for higher availability.
Downside of a Redis cluster is, that the mesh stops working, if a master node may crash.
Also, it does not _"guarantee strong consistency"_ ("Redis Cluster Tutorial – Redis Cluster consistency guarantees", 2018), this happens if a node answers to the client with a success, but the write action fails by a cluster node.  

To fight these downsides, Redis introduced Sentinel and is described by Joe Engel (2015) as _"automatic high availability […] solution for Redis"_ ("Introduction to Redis Sentinel").
Sentinel can detect nodes in a cluster, which are offline.
When this occurs, a message is send to every appended slave node informing them, their master node is offline.
With an election algorithm the remaining nodes select a new master node to replace the missing master.
With these two technologies, Redis appears to be safe method of storing data, while maintaining fast data handling because of in-memory efficiency.  

### 4.3.2 Administration

Regarding further administrative features, security is not a big focus of Redis, as describe in the article ("Redis Security", 2018).
In general, a Redis database is designed to be accessible only by trusted clients.
Network security can only be achieved, if implementing applications are bind to the server, i.e. only the application server can directly access the Redis database.
_"Redis does not try to implement Access Control"_ ("Redis Security", 2018), there exists a simple authentication by password, but it is saved in plain text inside the configuration file.
Further Redis does not support data encryption.  

Other features are for example latency monitoring, including latency hooks, time series recording and a reporting engine ("Redis latency monitoring framework", 2018).
All together can be said, Redis is a fast and simple database system for key-value pairs.
It implements lot of use cases, some of those will be described in the next chapter.
With some high availably and administrative features it can perform well in a productive system.

# References
Bennage C. & Wasson M. (2017). _Event-driven architecture style_.  
Retrieved from https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/event-driven.  
Viewed 27 February 2018.  

Engel, J. (2015). _Introduction to Redis Sentinel_.  
Retrieved from https://www.objectrocket.com/blog/how-to/introduction-to-redis-sentinel.  
Viewed 1 March 2018.  

Mani Chandy, K. (2006) _Event-Driven Applications: Costs, Benefits and Design Approaches_.  
Retrieved from http://docplayer.net/15630715-Event-driven-applications-costs-benefits-and-design-approaches-gartner-application-integration-and-web-services-summit-2006.html.  
Viewed 27 February 2018.  

NodeJS Landing Page. (2018).  
Retrieved from https://nodejs.org/en/  
Viewed 27 February 2018.  

_Redis Cluster Tutorial_. (2018).  
Retrieved from https://redis.io/topics/cluster-tutorial.  
Viewed 1 March 2018.

_Redis latency monitoring framework_. (2018).  
Retrieved from https://redis.io/topics/latency-monitor.  
Viewed 1 March 2018.

_Redis Security_. (2018).  
Retrieved from https://redis.io/topics/security.  
Viewed 1 March 2018.

Sanfilippo, S. (2017). _Readme of Redis GitHub_.  
Retrieved from https://github.com/antirez/redis/blob/4.0/README.md.  
Viewed 27 February 2018.

Sanfilippo, S. (2012). _Redis persistence demystified_.  
Retrieved from http://oldblog.antirez.com/post/redis-persistence-demystified.html.  
Viewed 27 February 2018.

_The full list of commands_. (2018).  
Retrieved from https://redis.io/commands.  
Viewed 1 March 2018.
