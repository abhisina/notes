---
theme : "simple"
transition: "slide"
highlightTheme: "zenburn"
logoImg: "gfs.png"
slideNumber: true
title: "Google File System"
customTheme : "local"
previewLinks : true

---

Google File System

---

Design Goals
- Componenet failures are norm rather than exception
- MultiGB files are common for day to day applications
- Files are changed mostly by appending contents at the end
- Make APIs which gives more flexibility than Linux VFS system
- Build on top of commodity hardware

---

Optimization Use case goals
- Large streaming reads & small random reads
- Often sequential appends and seldom writes in between
- Well defined semantics for multiple concurrent appends to the same file
- May act as producer consumer queues
- High sustained bandwidth >>> low latency

---

Components of cluster
- Master & shadow masters
- Chunk servers
- Clients

---

Architecture

![alt text](architecture.png "GFS architecture" =800x)

---

Master & Shadow Masters
::: block
- 3 types of metadata:
  - Mapping between files and their chunks
  - File and chunk namespaces
  - Location of each chunk's replicas
- 64 bytes of Metadata for each 64 MB chunk (prefix compression)
- BTree format metabdata backed by Operational log
- Clients are acked for operations only after operational log is committed locally and its shadow masters
- Shadow masters can help in read only operations
- Maintain good replication factor and balance between chunkservers.

{style=font-size:75%;}
:::

---

Chunkservers
- Store 64MB chunks
- Deals directly with clients
- Default replication factor is 3 but can be changed
- Checksum on each read - 2 bit checksum per 64 KB block
- Maintain LRU cache for file mutate operations
- Maintain worker processes for MapReduce

---

Client operations supported
- Open
- Read
- Write
- Close
- Append
- Snapshot
- Delete

---

Write

![alt text](write.png "Write control sequence" =650x)

---

gfs v2 (Colossus)
- Automatic sharding of metadata layer
- Data typically written using Reed-Solomon code
- Client driven replication
- Better metrics in terms of MTTF (Mean Time To failure)

---

FIN