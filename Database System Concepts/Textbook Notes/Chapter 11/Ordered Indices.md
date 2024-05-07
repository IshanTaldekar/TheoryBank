---
tags:
  - "#interesting"
---
==To gain fast random access to records in a file, we can use an index structure.== ==Each index structure is associated with a particular search key.== ==Just like the index of a book or a library catalog, an ordered index stores the values of the search key in sorted order, and associates with each search key the records that contain it.==

==The records in the indexed file may themselves be stored in some sorted order, just as books in a library are stored according to some attribute such as the Dewey decimal number.== ==A file may have several indices, on different search keys. If the file containing the records is sequentially ordered, an **clustering index** is an index whose search key also defines the sequential order of the file.== ==Clustering indices are also called **primary indices**; the term primary index may appear to denote an index on a primary key, but such indices can in fact be built on any search key.== ==The search key of a clustering index is often the primary key, although that is not necessarily so.== ==Indices whose search key specifies an order different from the sequential order of the file are called **non-clustering indices**, or **secondary** indices.== The term "clustered" and "non-clustered" are often used in place of "clustering" and "non-clustering".

==In the next sections, we assume that all files are ordered sequentially on some search key. Such files, with a clustering index on the search key are called **index-sequential files**.== They represent one of the oldest index schemes used in database systems. ==They are designed for applications that require both sequential processing of the entire file and random access to individual records.== 

![[Pasted image 20240504154216.png]]

Figure 11.1 shows a sequential file of *instructor* records taken from our university example. In the example of Figure 11.1, the records are stored in sorted order of instructor *ID*, which is used as the search key.

---
#### Explained
- **Indexed file and sorted order:**
	- An indexed file is a data file in a database system where records are organized and accessed using one or more indices.
	- Records within an indexed file may themselves be stored in a sorted order based on some attribute, similar to how books in a library are arranged according to attributes like the Dewey Decimal number or the author's last name.
- **Clustering index:**
	- A clustering index is an index in which the search key also defines the sequential order of the records in the file.
	- In other words, the records in the file are physically ordered based on the values of the search key used in the clustering index.
	- Clustering indices are sometimes called primary indices because they are typically built on the primary key of the records. However, they can be built on any search key, not just the primary key.
	- For example, if the primary key of a table is the student ID, a clustering index on the student ID would arrange the records in the file according to ascending or descending student ID values.
- **Non-clustering index:**
	- A non-clustering index, also known as a secondary index, is an index whose search key specifies an order different from the sequential order of the records in the file.
	- Unlike clustering indices, non-clustering indices do not dictate the physical order of records in the file.
	- These indices are useful for quickly locating records based on search keys other than the one used for clustering.
	- For example, a non-clustering index on the student's last name would allow for efficient retrieval of records based on the alphabetical order of last names, regardless of the physical order of records in the file.
- **Clustered vs. non-clustered:**
	- The terms "clustered" and "non-clustered" are often used interchangeably with "clustering" and "non-clustering", respectively, to describe indices based on whether the physical order of records matches the order defined by the index.

---
## Dense and Sparse Indices
==An **index entry**, or **index record**, consists of a search-key value and pointers to one or more records with that value as their search-key value. The pointer to a record consists of the identifier of a disk block and an offset within the disk block to identify the record within the block.==

![[Pasted image 20240504170958.png]]

There are two types of ordered indices that we can use:

- **Dense index:** ==In a dense index, an index entry appears for every search-key value in the file.== ==In a dense clustering index, the index record contains the search-key value and a pointer to the first data record with that search-key value. The rest of the records with the same search-key value would be stored sequentially after the first record, since, because the index is a clustering one, records are sorted on the same search key.==
  
  ==In a dense non-clustering index, the index must store a list of pointers to all records with the same search-key value.==
- **Sparse index:** ==In a sparse index, an index entry appears for only some of the search-key values.== ==Sparse indices can be used only if the relation is stored in sorted order of the search key, that is, if the index is a clustering index.== ==As is true in dense indices, each index entry contains a search-key value and a pointer to the first data record with the search-key value. To locate a record, we find the index entry with the largest search-key value that is less than or equal to the search-key value for which we are looking. We start at the record pointed to by that index entry, and follow the pointers in the file until we find the desired record.==

![[Pasted image 20240504171040.png]]

Figures 11.2 and 11.3 show dense and sparse indices, respectively, for the *instructor* file. Suppose that we are looking up the record of instructor with *ID* "22222". Using a dense index of Figure 11.2, we follow the pointer directly to the desired record. Since *ID* is a primary key, there exists only one such record and the search is complete. If we are using the sparse index (Figure 11.3), we do not find an index entry for "22222". Since the last entry (in numerical order) before "22222" is "10101", we follow that pointer. We then read the *instructor* file in sequential order until we find the desired record.

![[Pasted image 20240504171929.png]]

Consider a (printed) dictionary. The header of each page lists the first word alphabetically on that page. The words at the top of each page of the book index together form a sparse index on the contents of the dictionary pages.

![[Pasted image 20240504171952.png]]

As another example, suppose that the search-key value is not a primary key. Figure 11.4 shows a dense clustering index for the *instructor* file with the search key being *dept_name*. Observe that in this case the *instructor* file is sorted on the search key *dept_name*, instead of *ID*, otherwise the index on *dept_name* would be a non-clustering index. Suppose that we are looking up records for the History department. Using the dense index of Figure 11.4, we follow the pointer directly in that record to locate the next record in search-key (*dept_name*) order. We continue processing records until we encounter a record for a department other that History.

As we have seen, it ==is generally faster to locate a record if we have a dense index rather than a sparse index. However, sparse indices have advantages over dense indices in that they require less space and they impose less maintenance overhead for insertions and deletions.==

There is a trade-off that the system designer must make between access time and space overhead. ==Although the decision regarding this trade-off depends on the specific application, a good compromise is to have a sparse index with one entry per block.== ==The reason this design is a good trade-off is that the dominant cost in processing a database request is the time that it takes to bring a block from disk to main memory. Once we have brought in the block, the time to scan the entire block is negligible.== ==Using this sparse index, we locate the block containing the record that we are seeking. Thus, unless the record is on an overflow block (see [[Organization of Records in Files]]), we minimize block accesses while keeping the size of the index (and thus our space overhead) as small as possible.==

For the preceding technique to be fully general, we must consider the case where records for one search-key value occupy several blocks. It is easy to modify our scheme to handle this situation.
## Multi-level Indices
Suppose we build a dense index on a relation with 1,000,000 tuples. Index entries are smaller than data records, so let us assume that 100 index entries fit on a 4 kilobyte block. Thus, our index occupies 10,000 blocks. If the relation instead had 100,000,000 tuples, the index would instead occupy 1,000,000 blocks, or 4 gigabytes of space. Such large indices are stored as sequential files on disk.

==If an index is small enough to be kept entirely in main memory, the search time to find an entry is low. However, if the index is so large that not all of it can be kept in memory, index blocks must be fetched from disk when required. (Even if an index is smaller than the main memory of a computer, main memory is also required for a number of other tasks, so it is not possible to keep the entire index in memory.) The search for an entry in the index then requires several disk-block reads.==

==Binary search can be used on the index file to locate an entry, but the search still has a large cost. If the index would occupy *b* blocks, binary search requires as many as $\lceil log_2(b) \rceil$ blocks to be read. ($\lceil x \rceil$ denotes the least integer that is greater than or equal to x; that is, we round upward.) For a 10,000-block index, binary search requires 14 block reads. On a disk system where a block read takes on average 10 milliseconds, the index search will take 140 milliseconds. This may not seem much, but we would be able to carry out only seven index searches a second, whereas a much more efficient search mechanism would let us carry out far more searches per second, as we shall see shortly. Note that, if overflow blocks have been used, binary search is not possible. In that case, a sequential search is typically used, and that requires *b* block reads, which will take even longer. Thus, the process of searching a large index may be costly.==

![[Pasted image 20240504180940.png]]

==To deal with this problem, we treat the index just as we would treat any other sequential file, and construct a sparse outer index on the original index, which we now call the inner index, as shown in Figure 11.5. Note that the index entries are always in sorted order, allowing the outer index to be sparse. To locate a record, we first use binary search on the outer index to find the record for the largest search-key value less than or equal to the one that we desire. The pointer points to a block of the inner index. We scan this block until we find the record that has the largest search-key value less than or equal to the one that we desire. The pointer in this record points to the block of the file that contains the record for which we are looking.==

==In our example, an inner index with 10,000 blocks would require 10,000 entries in the outer index, which would occupy just 100 blocks. If we assume that the outer index is already in main memory, we would read only one index block for a search using a multi-level index, rather than the 14 blocks we read with binary search. As a result, we can perform 14 times as many index searches per second.==

==If our file is extremely large, even the outer index may grow too large to fit in main memory. With a 100,000,000 tuple relation, the inner index would occupy 1,000,000 blocks, and the outer index occupies 10,000 blocks, or 40 megabytes. Since there are many demands on main memory, it may not be possible to reserve that much main memory just for this particular outer index. In such a case, we can create yet another level of index. Indeed, we can repeat this process as many times as necessary. Indices with two or more levels are called **multi-level** indices. Searching for records with multi-level index requires significantly fewer I/O operations than does searching for records by binary search.==

Multi-level indices are closely related to tree structures, such as the binary trees used for in-memory indexing. 
## Index Updates
==Regardless of what form of index is used, every index must be updated whenever a record is either inserted into or deleted from the file. Further, in case a record in a file is updated, any index whose search-key attribute is affected by the update must also be updated; for example, if the department of an instructor is changed, an index on the *dept_name* attribute of the *instructor* must be updated correspondingly.== ==Such a record update can be modeled as a deletion of the old record, followed by an insertion of the new value of the record, which results in an index deletion followed by an index insertion. As a result we only need to consider insertion and deletion on an index, and do not need to consider updates explicitly.==

We first describe algorithms for updating single-level indices.

- **Insertion**. First, the system performs a lookup using the search-key value that appears in the record to be inserted. The actions the system takes next depend on whether the index is dense or sparse:
	- Dense indices:
		1. ==If the search-key value does not appear in the index, the system inserts an index entry with the search-key value in the index at the appropriate position.==
		2. Otherwise the following actions are taken:
			- ==If the index entry stores pointers to all records with the same search-key value, the system adds a pointer to the new record in the index entry.==
			- ==Otherwise, the index entry stores a pointer to only the first record with the search-key value. The system then places the record being inserted after the other records with the same search-key values.==
	- Sparse indices: ==We assume that the index stores an entry for each block. If the system creates a new block, it inserts the first search-key value  (in search-key order) appearing in the new block into the index. On the other hand, if the new record has the least search-key value in its block, the system updates the index entry pointing to the block; if not, the system makes no change to the index.==
- **Deletion.** ==To delete a record, the system first looks up the record to be deleted. The actions the system takes next depend on whether the index is dense or sparse:==
	- Dense indices:
		1. ==If the deleted record was the only record with its particular search-key value, then the system deletes the corresponding index entry from the index.==
		2. Otherwise the following actions are taken:
			- ==If the index entry stores pointers to all records with the same search-key value, the system deletes the pointer to the deleted record from the index entry.==
			- ==Otherwise, the index entry stores a pointer to only the first record with the search-key value. In this case, if the deleted record was the first record with the search-key value, the system updates the index entry to point to the next record.==
	- Sparse indices:
		1. ==If the index does not contain an index entry with the search-key value of the deleted record, nothing needs to be done to the index.==
		2. ==Otherwise the system takes the following actions:==
			- ==If the deleted record was the only record with its search key, the system replaces the corresponding index record with an index record for the next search-key value (in search-key order). If the next search-key value already has an index entry, the entry is deleted instead of being replaced.==
			- ==Otherwise, if the index entry for the search-key value points to the record being deleted, the system updates the index entry to point to the next record with the same search-key value.==

Insertion and deletion algorithms for multi-level indices are a simple extension of the scheme just described. On deletion or insertion, the system updates the lowest-level index described. As far as the second level is concerned, the lowest-level index is merely a file containing records - thus, if there is any change in the lowest-level index, the system updates the second-level index as described. The same technique applies to further levels of the index, if there are any.
## Secondary Indices
==Secondary indices must be dense, with an index entry for every search-key value and a pointer to every record in the file. A clustering index may be sparse, storing only some of the search-key values, since it is always possible to find records with intermediate search-key values by a sequential access to a part of the file, as described earlier. If a secondary index stores only some of the search-key values, records with intermediate search-key values may be anywhere in the file and, in general, we cannot find them without searching the entire file.==

==A secondary index on a candidate key looks like a dense clustering index, except that the records pointed to by successive values in the index are not stored sequentially. In general, however, secondary indices may have a different structure from clustering indices. If the search key of a clustering index is not a candidate key, it suffices if the index points to the first record with a particular value for the search key, since the other records can be fetched by a sequential scan of the file.==

==In contrast, if the search key of a secondary index is not a candidate key, it is not enough to point to just the first record with each search-key value. The remaining records with the same search-key value could be anywhere in the file, since the records are ordered by the search key of the clustering index, rather than by the search key of the secondary index. Therefore, a secondary index must contain pointers to all the records.==

![[Pasted image 20240504205746.png]]

==We can use an extra level of indirection to implement secondary indices on search keys that are not candidate keys. The pointers in such a secondary index do not point directly to the file. Instead, each points to a bucket that contains pointers to the file. Figure 11.6 shows the structure of secondary index that uses an extra level of indirection on the *instructor* file, on the search key *salary*.==

==A sequential scan in cluster index order is efficient because records in the file are stored physically in the same order as the index order. However, we cannot (except in rare special cases) store a file physically ordered by both the search key of the clustering index and the search key of a secondary index.==

==Because secondary-key order and physical-key order differ, if we attempt to scan the file sequentially in secondary-key order, the reading of each record is likely to require the reading of a new block from disk, which is very slow.== 

==The procedure described earlier for deletion and insertion can also be applied to secondary indices; the actions taken are those described for dense indices storing a pointer to every record in the file. If a file has multiple indices, whenever the file is modified, *every* index must be updated.==

==Secondary indices improve the performance of queries that use keys other than the search key of the clustering index. However, they impose a significant overhead on modification of the database. The designer of a database decides which secondary indices are desirable on the basis of an estimate of the relative frequency of queries and modification.==

---
#### Automatic Creation of Indices
If a relation is declared to have a primary key, most database implementations automatically create an index on the primary key. Whenever a tuple is inserted into the relation, the index can be used to check that the primary key constraint is not violated (that is, there are no duplicates on the primary key value). Without the index on the primary key, whenever a tuple is inserted, the entire relation would have to be read to ensure that the primary-key constraint is satisfied.

---
## Indices on Multiple Keys
Although the examples we have seen so far have had a single attribute in a search key, in general a search key can have more than one attribute. A search key containing more than one attribute is referred to as a **composite search key**. The structure of the index is the same as that of any other index, the only difference being that the search key is not a single attribute, but rather a list of attributes. The search key can be represented as a tuple of values, of the form $(a_1,\ ...,\ a_n)$, where the indexed attributes are $A_1,\ ...,\ A_n$. The ordering of search-key values is the *lexicographic ordering*. For example, for the case of two attribute search keys, $(a_1,a_2)\ <\ (b_1, b_2)$ if either $a_1\ <\ b_1$ or $a_1\ =\ b_1$ and $a_2\ <\ b_2$. Lexicographic ordering is basically the same as alphabetic ordering of words.

As an example, consider an index on the *takes* relation, on the composite search key (*course_id, semester, year*). Such an index would be useful to find all students who have registered for a particular course in a particular semester/year. An ordered index on a composite key can also be used to answer several other kinds of queries efficiently.
## Related Articles
- [[Chapter 11 - Indexing and Hashing]]