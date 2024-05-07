---
tags:
  - interesting
  - low-level
  - memory
  - file-organization
  - pages
  - algorithms
---
- A database is mapped into a number of different files that are maintained by the underlying operating system. These files reside permanently on disks. A **file** is organized logically as a sequence of records. These records are mapped onto [[Disk Blocks|disk blocks]]. Files are provided as a basic construct in operating systems, so we shall assume the existence of an underlying *file system*. 
- ==Each file is also logically partitioned into a fixed-length storage units called **blocks**, which are **the units of both storage allocation and data transfer**.== 
- In the context of databases, *records* and *blocks* serve distinct purposes. Records refer to the logical units of data within a file. These are the individual pieces of data that the file contains. For example, in a database, each record might represent a single entry or row of information. Blocks, on the other hand, are physical units of storage on the disk. They represent the smallest amount of data that can be read from or written to the disk at one time. When a file is stored on disk, its records are typically divided into blocks to facilitate efficient storage and retrieval. 
- ==A block may contain several records; the exact set of records that a block contains is determined by the form of physical data organization being used. We shall assume that *no record is larger than a block*. This assumption is realistic for most data-processing applications, such as our university example. We briefly discuss how to handle such large data items later, in Section 10.5.2, by storing large data items separately, and storing a pointer to the data item in the record.==
- ==In addition, we shall require that *each record is entirely contained in a single block*; that is, no record is contained partly in one block, and partly in another. This restriction simplifies and speeds up access to data items.== 
- In a relational database, tuples of distinct relations are generally of different sizes. One approach to mapping the database to files is to use several files, and to store records of only one fixed length in any given file. An alternative is to structure our files so that we can accommodate multiple lengths for records; however, files of fixed-length records are easier to implement than are files of variable-length records. Many of the techniques used for the former can be applied to the variable-length cases. 
	- ==To explain this better, there are two types of records: fixed-length and variable-length records. With the fixed-length record approach, each record in the file has a predetermined, fixed size. Regardless of the actual data contained within the record, it occupies the same amount of space in the file. This makes it easy to calculate the position of each record within the file and allow for efficient direct access. In contrast, with the variable-length record approach, records can have varying sizes. The size of each record depends on the amount of data it contains. This flexibility allows for more efficient storage of data, especially when records have different sizes, but it also introduces complexities in managing and accessing the data.== 
	- ==When designing a database management system, one of the considerations is how to store the data on disk efficiently. This involves mapping the logical structure of the database (relations, tuples, attributes) to physical storage on disk in the form of files. One approach, as mentioned in the paragraph above, is to use several files, each containing records of only one fixed length. This simplifies the file organization and access because all records in a file have the same size, making it easier to calculate offsets and perform direct access. Another approach is to structure files to accommodate multiple lengths for records, allowing for variable-length records. However, this introduces complexities in managing the storage and accessing records due to varying sizes.== 
	- ==Files containing fixed-length records are generally easier to implement compared to files containing variable-length records. This is because fixed-length records allow for simpler calculation of record offsets and easier management of space allocation within the file. Techniques used for managing files with fixed-length records can often be adapted to handle files with variable-length records. However, additional considerations and mechanisms are required to manage variable-length records efficiently, such as using pointers or delimiters to indicate the start and end of each record.==
## ==Fixed-Length Records==
As an example, let us consider a file of *instructor* records for our university database. Each record of this file is defined (in pseudocode) as:

![[Pasted image 20240503015450.png]]

Assume that each character occupies 1 byte and that numeric (8, 2) occupies 8 bytes. Suppose that instead of allocating a variable amount of bytes for the attributes *ID, name,* and *department*, we allocate the maximum number of bytes that each attribute can hold. Then, the *instructor* record is 53 bytes long. A simple approach is to use the first 53 bytes for the first record, the next 53 bytes for the second record, and so on (Figure 10.4). However, there are two problems with this simple approach:

![[Pasted image 20240503015703.png]]

1. Unless the block size happens to be a multiple of 53 (which is unlikely), some records will cross block boundaries. That is, part of the record will be stored in one block and part in another. It would thus require two block accesses to read or write such a record.
2. It is difficult to delete a record from this structure. The space occupied by the record to be deleted must be filled with some other record of the file, or we must have a way of marking deleted records so that they can be ignored.

To avoid the first problem, we allocate only as many records to a block as would fit entirely in the block (this number can be computed easily by dividing the block size by the record size, and discarding the fractional part). Any remaining bytes of each block are left unused.

![[Pasted image 20240503020101.png]]

When a record is deleted, we could move the record that came after it into the space formerly occupied by the deleted record, and so on, until every record following the deleted record has been moved ahead (Figure 10.5). Such an approach requires moving a large number of records. It might be easier simply to move the final record of the file into the space occupied by the deleted record (Figure 10.6).

![[Pasted image 20240503020136.png]]

It is undesirable to move records to occupy the space freed by a deleted record, since doing so requires additional block accesses. Since insertions tend to be more frequent than deletions, it is acceptable to leave open the space occupied by the deleted record, and wait for a subsequent insertion before reusing the space. A simple marker on a deleted record is not sufficient, since it is hard to find this available space when an insertion is being done. Thus, we need to introduce an additional structure.

At the beginning of the file, we allocate a certain number of bytes as a **file header**. The header will contain a variety of information about the file. For now, all we need to store is the address of the first record whose contents are deleted. We use this first record to store the address of the second available record, and so on. Intuitively, we can think of these stored addresses as *pointers*, since they point to the location of a record. The deleted records, thus form a linked list, which is often referred to as a **free list**. Figure 10.7 shows the file of Figure 10.4, with the free list, after records 1, 4, and 6 have been deleted.

![[Pasted image 20240503025638.png]]

On insertion of a new record, we use the record pointed to by the header. We change the header pointer to point to the next available record. If no space is available, we add the new record to the end of the file.

Insertion and deletion for files of fixed-length records are simple to implement, because the space made available by a deleted record is exactly the space needed to insert a record. If we allow records of variable length in a file, this match no longer holds. An inserted record may not fit in the space left free by a deleted record, or it may fill only part of that space.
## Variable-Length Records
Variable-length records arise in database systems in several ways:

- Storage of multiple record types in a file.
- Record types that allow variable lengths for one or more fields.
- Record types that allow repeating fields, such as arrays or multisets.

Different techniques for implementing variable-length records exist. Two different problems must be solved by any such technique:

- How to represent a single record in such a way that individual attributes can be extracted easily.
- How to store variable-length records within a block, such that records in a block can be extracted easily.

The representation of a record with variable-length attributes typically has two parts: an initial part with fixed length attributes, followed by data for variable length attributes. Fixed-length attributes, such as numeric values, dates, or fixed-length character strings are allocated as many bytes as required to store their value. Variable-length attributes, such as `varchar` types, are represented in the initial part of the record by a pair (*offset, length*), where *offset* denotes where the data attribute begins within the record, and *length* is the length in bytes of the variable-sized attribute. The value for these attributes are stored consecutively, after the initial fixed-length part of the record. Thus, the initial part of the record stores a fixed size of information about each attribute, whether it is fixed-length or variable-length.

An example of such a record representation is shown in Figure 10.8. The figure shows an *instructor* record, whose first three attributes *ID, name,* and *dept_name* are variable-length strings, and whose fourth attribute *salary* is a fixed-sized number. We assume that the offset and length values are stored in two bytes each, for a total of 4 bytes per attribute. The *salary* attribute is assumed to be stored in 8 bytes, and each string takes as many bytes as it has characters.

![[Pasted image 20240503074512.png]]

The figure also illustrates the use of a **null bitmap**, which indicates which attributes of the record have a null value. In this particular record, if the salary were null, the fourth bit of the bitmap would be set to 1, and the *salary* value stored in bytes 12 through 19 would be ignored. Since the record has four attributes, the null bitmap for this record fits in 1 byte, although more bytes may be required with more attributes. ==In some representations, the null bitmap is stored at the beginning of the record, and for attributes that are null, no data (value, or offset/length) are stored at all. Such a representation would save some storage space, at the cost of extra work to extract attributes of the record. This representation is particularly useful for certain applications where records have a large number of fields, most of which are null.==

![[Pasted image 20240503075450.png]]

==We next address the problem of storing variable-length records in a block. The **slotted-page structure** is commonly used for organizing records within a block, and is shown in Figure 10.9 (here page is synonymous with "block"). There is a header at the beginning of each block, containing the following information:==

1. ==The number of record entries in the header.==
2. ==The end of free space in the block.==
3. ==An array whose entries contain the location and size of each record.====

==The actual records are allocated *contiguously* in the block, starting from the end of the block. The free space in the block is contiguous, between the final entry in the header array, and the first record. If a record is inserted, space is allocated for it at the end of free space, and an entry containing its size and location is added to the header.

==If a record is deleted, the space that it occupies is freed, and its entry is set to *deleted* (its size is set to -1, for example). Further, the records in the block before the deleted record are moved, so that the free space created by the deletion gets occupies, and all free space is again between the final entry in the header array and the first record. The end-of-free-space pointer in the header is appropriately updated as well. Records can be grown or shrunk by similar techniques, as long as there is space in the block. The cost of moving the records is not too high, since the size of the block is limited: typically values are around 4 to 8 kilobytes.==

==The slotted-page structure requires that there be no pointers that point directly to records. Instead, pointers must point to the entry in the header that contains the actual location of the record. This level of indirection allows records to be moved to prevent fragmentation of space inside a block, while supporting indirect pointers to the record. Explained more clearly, in slotted-page structure, the pointers in the header entries provide indirection to the actual locations of the records within the data section of the block. Instead of directly pointing to the records themselves, pointers **outside** the block (e.g., in index structures or other data structures) point to these header entries. By referencing the header entries rather than pointing to records, the system gains flexibility in managing the storage of records within the block. It allows for efficient record movement without invalidating external pointers and supports stable references to records, even when their positions within the block change.==

Databases often store data that can be much larger than a disk block. For instance, an image or an audio recording may be multiple megabytes in size, while a video object may be multiple gigabytes in size. Recall that SQL supports the types **blob** and **clob**, which store binary and character large objects.

Most relational databases restrict the size of a record to be no larger than the size of a block, to simplify buffer management and free-space management. Large objects are often stored in a special file (or collection of files) instead of being stored with the other (short) attributes of records in which they occur. A (logical) pointer to the object is then stored in the record containing the large object. Large objects are often represented using $B^+$-tree file organizations. $B^+$-tree file organizations permits us to read the entire object, or specified byte ranges in the object, as well as to insert and delete parts of the object.
## Related Articles
- [[Chapter 10 - Storage and File Structure]]


