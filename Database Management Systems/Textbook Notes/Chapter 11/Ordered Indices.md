==To gain fast random access to records in a file, we can use an index structure.== ==Each index structure is associated with a particular search key.== ==Just like the index of a book or a library catalog, an ordered index stores the values of the search key in sorted order, and associates with each search key the records that contain it.==

The records in the indexed file may themselves be stored in some sorted order, just as books in a library are stored according to some attribute such as the Dewey decimal number. A file may have several indices, on different search keys. If the file containing the records is sequentially ordered, as **clustering index** is an index whose search key also defines the sequential order of the file. Clustering indices are also called **primary indices**; the term primary index may appear to denote an index on a primary key, but such indices can in fact be built on any search key. The search key of a clustering index is often the primary key, although that is not necessarily so. Indices whose search key specifies an order different from the sequential order of the file are called **non-clustering indices**, or **secondary** indices. The term "clustered" and "non-clustered" are often used in place of "clustering" and "non-clustering".

In the next sections, we assume that all files are ordered sequentially on some search key. Such files, with a clustering index on the search key are called **index-sequential files**. They represent one of the oldest index schemes used in database systems. They are designed for applications that require both sequential processing of the entire file and random access to individual records. 

Figure 11.1 shows a sequential file of *instructor* records taken from our university example. In the example of Figure 11.1, the records are stored in sorted order of instructor *ID*, which is used as the search key.

![[Pasted image 20240504154216.png]]



