In [[File Organization]], we looked at how records are represented in a file structure within blocks. A relation is a set of records. Given a set of records, there are several ways of organizing them in a file:

- ==**Heap file organization:** Any record can be placed anywhere in the file where there is space for the record. There is no ordering of records. Typically, there is a single file for each relation.==
- **Sequential file organization:** Records are stored in sequential order, according to the value of a "search key" of each record. 
- **Hashing file organization:** A hash function is computed on some attribute of each record. The result of the hash function specifies in which block of the file the record should be placed. This organization is closely related to indexing structures.

Generally, a separate file is used to store the records of each relation. However, in ==a **multi-table clustering organization**, records of several different relations are stored in the same file; further, related records of the different relations are stored on the same block, so that one I/O operation fetches related records from all the relations. For example, records of the two relations can be considered to be related if they would match in a join of the two relations.==

## Heap File Organization
In heap file organization, records are stored in the file without any particular order or arrangement. When a new record is inserted into the file, it can be placed at any available space within the file where it fits. This means that records are appended to the end of the file or inserted into any available gaps between existing records, without regard to their logical or physical order.

Unlike in some other file organization methods (such as sorted or clustered file organizations), records in a  heap file are not arranged based on any specific criteria, such as key values or timestamps. Records are stored in the order in which they are inserted into the file, without any attempt to impose a particular order or sequence.

In a database, a "relation" typically refers to a table. For each table (relation) in the database schema, there is usually a corresponding heap file where the records of that table are stored. This means that each table's records are stored independently in their own file, using the heap file organization method.
## Sequential File Organization
==A **sequential file** is designed for efficient processing of records in sorted order based on some search key. A **search key** is any attribute or set of attributes; it need not be the primary key, or even a superkey. To permit fast retrieval of records in search-key order, we chain together records by pointers. The pointer in each record points to the next record in search-key order. Furthermore, to minimize the number of block accesses in sequential file processing, we store records physically in search-key order, or as close to search-key order as possible.==

![[Pasted image 20240503082535.png]]

Figure 10.10 shows a sequential file of *instructor* records taken from our university example. In that example, the records are stored in search-key order, using *ID* as the search key.

The sequential file organization allows records to be read in sorted order; that can be useful for display purposes, as well as for certain query-processing algorithms.

==It is difficult, however, to maintain physical sequential order as records are inserted and deleted, since it is costly to move many records as a result of a single insertion or deletion. We can manage deletions by using pointer chains, as we saw previously. For insertions, we apply the following rules:==

1. ==Locate the record in the file that comes before the record to be inserted in search-key order.==
2. ==If there is a free record (that is, space left after a deletion) within the same block as this record, insert the new record there. Otherwise, insert the new record in an *overflow block*. In either case, adjusting the pointers so as to chain together the records in search-key order.==

![[Pasted image 20240503083144.png]]

==Figure 10.11 shows the file of Figure 10.10 after the insertion of the record (322222, Verdi, Music, 48000). The structure in Figure 10.11 allows fast insertion of new records, but forces sequential file-processing applications to process records in an order that does not match the physical order of the records.==

==If relatively few records need to be stored in overflow blocks, this approach works well. Eventually, however, the correspondence between search-key order and physical order may be totally lost over a period of time, in which case sequential processing will become much less efficient. At this point, the file should be **reorganized** so that it is once again physically in sequential order. Such reorganizations are costly, and must be done during times when the system load is low. The frequency with which reorganizations are needed depends on the frequency of insertion of new records. In the extreme case in which insertions rarely occur, it is possible always to keep the file in physically sorted order. In such a case, the pointer field in Figure 10.10 is not needed.==

## Multi-table Clustering File Organization
==Many relational database systems store each relation in a separate file==, so that they can take full advantage of the file system that the operating system provides. ==Usually, tuples of a relation can be represented as fixed-length records. Thus, relations can be mapped to a simple file structure.== ==This simple implementation of a relational database is well suited to low-cost database implementations as in, for example, embedded systems or portable devices. In such systems, the size of the database is small, so little is gained from a sophisticated file structure. Furthermore, in such environments, it is essential that the overall size of the object code for the database be small. A simple file structure reduces the amount of code needed to implement the system.==

==This simple approach to relational database implementation becomes less satisfactory as the size of the database increases. We have seen that there are performance advantages to be gained from careful assignment of records to blocks, and from careful organization of the block themselves. Clearly, a more complicated file structure may be beneficial, even if we retain the strategy of storing each relation in a separate file.==

==However, many large-scale database systems do not rely directly on the underlying operating system for file management. Instead, one large operating system file is allocated to the database system. The database system stores all relations in this one file, and manages the file itself.==

==Even if multiple relations are stored in a single file, by default most databases store records of only one relation in a given block.== ==This simplifies data management. However, in some cases it can be useful to store records of more than one relation in a single block. To see the advantage of storing records of multiple relations in one block, consider the following SQL query for the university database:==

![[Pasted image 20240503083930.png]]

==This query computes a join of the *department* and *instructor* relations. Thus, for each tuple of *department*, the system must locate the *instructor* tuples with the same value for *dept_name*.== Ideally, these records will be located with the help of *indices*. ==Regardless of how these records are located, however, they will need to be transferred from disk into main memory. In the worst case, each record will reside on a different block, forcing us to do a one block read for each record required by the query.==

![[Pasted image 20240503084641.png]]

==As a concrete example, consider the *department* and *instructor* relations of Figure 10.12 and 10.13, respectively (for brevity, we include only a subset of the tuples of the relation we have used thus far). In Figure 10.14, we show a file structure designed for efficient execution of queries involving the natural join of *department* and *instructor*. The *instructor* tuples for each *ID* are stored near the *department* tuple for the corresponding *dept_name*. This structure mixes together tuples of two relations, but allows for efficient processing of the join. When a tuple of the *department* relation is read, the entire block containing that tuple is copied from disk into main memory. Since the corresponding *instructor* tuples are stored on disk near the *department* tuple, the block containing the *department* tuple contains tuples of the *instructor* relation needed to process the query. If a department has so many instructors that the *instructor* records do not fit one block, the remaining records appear on nearby blocks.==

![[Pasted image 20240503085055.png]]

A **multi-table clustering file organization** is a file organization, such as that illustrated in Figure 10.14, that stores related records of two or more relations in each block. Such a file organization allows us to read records that would satisfy the join condition using one block read. Thus, we are able to process this particular query more efficiently.

![[Pasted image 20240503085440.png]]

In the representation shown in Figure 10.14, the *dept_name* attribute is omitted from *instructor* records since it can be inferred from the associated *department* record; the attribute may be retained in some implementations, to simplify access to the attributes. We assume that each record contains the identifier of the relation to which it belongs, although this is not shown in Figure 10.14.

Our use of clustering of multiple tables into a single file has enhanced processing of a particular join (that of *department* and *instructor*), but it results in slowing processing of other types of queries. For example, 
![[Pasted image 20240503085454.png]]
==requires more block accesses than it did in the scheme under which we stored each relation in a separate file, since each block now contains significantly fewer *department* records. To locate efficiently all tuples of the *department* relation in the structure of Figure 10.14, we can chain together all the records of that relation using pointers, as in Figure 10.15.==

![[Pasted image 20240503085636.png]]

When multi-table clustering is to be used depends on the type of queries that the database designer believe to be most frequent. Careful use of multi-table clustering can produce significant performance gains in query processing.

#interesting #low-level #memory #file-organization #pages #algorithms