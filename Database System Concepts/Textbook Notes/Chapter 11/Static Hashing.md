---
tags:
  - interesting
  - low-level
  - "#hashing"
  - memory
  - algorithms
  - "#closed-hashing"
  - "#open-hashing"
---
==One disadvantage of sequential file organization is that we must access an index structure to locate data, or must use binary search, and that results in more I/O operations.== ==File organizations based on the technique of **hashing** allow us to avoid accessing the index structure. Hashing also provides a way of constructing indices.== 

In our description of hashing, we shall use ==the term **bucket** to denote a unit of storage that can store one or more records.== ==A bucket is typically a disk block, but could be chosen to be smaller or larger than a disk block.==

==Formally, let *K* denote the set of all search-key values, and let *B* denote the set of all bucket addresses. A **[[Hash Function|hash function]]** *h* is a function from *K* to *B*.== Let *h* denote a hash function.

==To insert a record with a search key $K_i$, we compute $h(K_i)$, which gives the address of the bucket for that record.== Assume now that there is space in the bucket to store the record. Then, the record is stored in that bucket.

==To perform a lookup on a search-key value $K_i$, we simply compute $h(K_i)$, then search the bucket with that address.== ==Suppose that two search keys, $K_5$ and $K_7$, have the same hash value; that is, $h(K_5)$ = $h(K_7)$. If we perform a lookup on $K_5$, the bucket $h(K_5)$ contains records with search-key values $K_5$ and records with search-key values $K_7$. Thus, we have to check the search-key value of every record in the bucket to verify that the record is the one that we want.==

==Deletion is equally straightforward. If the search-key value of the record to be deleted is $K_i$, we compute $h(K_i)$, then search the corresponding bucket for that record, and delete the record from the bucket.==

Hashing can be used for two different purposes. ==In a **hash file organization**, we obtain the address of the disk block containing a desired record directly by computing a function on the search-key value of the record. In a **hash index organization** we organize the search keys, with their associated pointers, into a hash file structure.==

---
#### Explained: Hash file organization and hash index organization
The difference between hash file organization and hash index organization lies in how they utilize hashing techniques for data storage and retrieval:

1. **Hash File Organization:**
	- In hash file organization, data records are stored directly in disk blocks, and the address of the disk block containing a desired record is obtained by computing a hash function on the search-key value of the record.
	- Each record is hashed using a hash function, which generates a hash value or hash code based on the search-key value of the record.
	- The hash value is then used as an index to determine the location of the disk block where the record should be stored or retrieved.
	- Records with different search-key values may hash to the same location, leading to collision resolution techniques such as chaining or open addressing to handle conflicts.
	- Hash function organization is typically used for direct access or random access scenarios, where fast retrieval of individual records based on their search keys is required.
2. **Hash Index Organization:**
	- In hash index organization, instead of storing data records directly in disk blocks, we organize the search-keys (and their associated pointers to data records) into a hash file structure.
	- Each search key is hashed using a hash function to generate a hash value.
	- The hash value serves as an index into the hash file, where the associated pointer or references to the data record is stored.
	- Unlike hash file organization, where data records are directly stored in disk blocks, hash index organization maintains a separate index structure that maps search keys to their corresponding data records. 
	- Hash index organization is commonly used in database systems to accelerate data retrieval operations, especially for large datasets, by providing efficient access to records based on their search keys.

In summary, hash file organization stores data records directly in disk blocks and uses hashing to determine the location of records, while hash index organization maintains a separate index structure that maps search keys to their associated data records using hashing techniques. 

---
## Hash Functions
==The worst possible hash function maps all search-key values to the same bucket.== Such a function is undesirable because all the records have to be kept in the same bucket. A lookup has to examine every such record to find the one desired. ==An ideal hash function distributes the stored keys uniformly across all the buckets, so that every bucket has the same number of records.==

==Since we do not know at design time precisely which search-key values will be stored in the file, we want to choose a hash function that assigns search-key values to buckets in such a way that the distribution has these qualities:==

- ==The distribution is *uniform*. That is, the hash function assigns each bucket the same number of search-key values from the set of *all* possible search-key values.==
- ==The distribution is *random*. That is, in the average case, each bucket will have nearly the same number of values assigned to it, regardless of the actual distribution of search-key values. More precisely, the hash value will not be correlated to any externally visible ordering on the search-key values, such as alphabetic ordering or ordering by the length of the search keys; the hash function will appear to be random.==

--- 
#### Explained: Characteristics of a good hash function
A good hash function exhibits the following properties:
1. **Uniform Distribution:**
	- A good hash function should distribute its outputs (hash codes) uniformly across its range of possible values.
	- This means that for a given input space, each possible output have roughly the same probability of occurring.
	- Achieving a uniform distribution helps prevent clustering of hash values and reduces the likelihood of collisions.
2. **Independence from Input Patterns (Randomness):**
	- The hash function should exhibit independence from any patterns or structures present in the input data.
	- In other words, small changes in the input data should result in significantly different hash values.
	- This property ensures that the hash function appears random with respect to the input data, making it difficult to predict the hash value for a given input.
3. **Avalanche Effect:**
	- The hash function should demonstrate the avalanche effect, where a small change in the input data produces a significant change in the resulting hash value.
	- This property ensures that even minor alterations in the input data lead to drastically different hash codes, enhancing the security and robustness of the hash function against attacks.
4. **Deterministic Output:**
	- Although the hash function behaves like a random function, it is deterministic, meaning that given the same input, it always produces the same output.

---
As an illustration of these principles, let us choose a hash function for the *instructor* file using the search key *dept_name*. The hash function that we choose must have the desirable properties not only on the example *instructor* file that we have been using, but also on an *instructor* file of realistic size for a large university with many departments.

==Assume that we decide to have 26 buckets, and we define a hash function that maps names beginning with the *i*th letter of the alphabet to the *i*th bucket. This hash function has the virtue of simplicity, but fails to provide a uniform distribution, since we expect more names to begin with such letters as B and R than Q and X, for example.==

==Now suppose that we want a hash function on the search key *salary*. Suppose that the minimum salary is $30,000 and the maximum salary is $130,000, and we use a hash function that divides the values into 10 ranges, $30,000-$40,000, $40,001-$50,000 and so on. The distribution of search-key values is uniform (since each bucket has the same number of different *salary* values), but is not random. Records with salaries between $60,001 and $70,000 are far more common than are records with salaries between $30,001 and $40,000. As a result, the distribution of records is not uniform - some buckets receive more records than others do. If the function has a random distribution, even if there are such correlations in the search keys, the randomness of the distribution will make it very likely that all buckets have roughly the same number of records, as long as each search key occurs in only a small fraction of the records. (If a single search key occurs in a large fraction of the records, the buckets containing it is likely to have more records than other buckets, regardless of the hash function used.)==

Typically hash functions perform computation on the internal binary machine representation of characters in the search key. A simple hash function of this type first computes the sum of the binary representations of the characters of a key, then returns the sum modulo the number of buckets.

![[Pasted image 20240506222621.png]]

Figure 11.23 shows the application of such a scheme, with eight buckets, to the *instructor* file, under the assumption that the *i*th letter in the alphabet is represented by the integer *i*.

==The following hash function is a better alternative for hashing strings. Let *s* be a string of length *n*, and let *s[i]* denote the *i*th byte of the string. The hash function is defined as:==

![[Pasted image 20240506215229.png]]

==The function can be implemented efficiently by setting the hash result initially to 0, and iterating from the first to the last character of the string, at each step multiplying the hash value by 31 and then adding the next character (treated as an integer). The above expression would appear to result in a very large number, but it is actually computed with fixed-size positive integers, the result of each multiplication and addition is thus automatically computed modulo the largest possible integer value plus 1. The result of the above function modulo the number of buckets can then be used for indexing.==

Hash functions require careful design. A bad hash function may result in lookup taking time proportional to the number of search keys in the file. A well-designed function gives an average-case lookup time that is a (small) constant, independent of the number of search keys in the file.
## Handling of Bucket Overflows
So far, we have assumed that, when a record is inserted, the bucket to which it is mapped has space to store the record. ==If the bucket does not have enough space, a **bucket overflow** is said to occur.== Bucket overflow can occur for several reasons:

- ==**Insufficient buckets.** The number of buckets, which we denote $n_B$, must be chosen such that $n_B > n_r/f_r$ where $n_r$ denotes the total number of records that will be stored and $f_r$ denotes the number of records that will fit in a bucket. This designation, of course, assumes that the total number of records is known when the hash function is chosen.==
- ==**Skew.** Some buckets are assigned more records than are others, so a bucket may overflow even when other buckets still have space. This situation is called bucket **skew**. Skew can occur for two reasons:==
	1. ==Multiple records may have the same search key.==
	2. ==The chosen hash function may result in nonuniform distribution of search keys.==

==So that the probability of bucket overflow is reduced, the number of buckets is chosen to be $(n_r/f_r) * (1 + d)$, where *d* is a fudge factor, typically around 0.2. Some space is wasted: About 20 percent of the space in the buckets will be empty. But the benefit is that the probability of overflow is reduced.==

![[Pasted image 20240506222718.png]]

==Despite allocation of a few more buckets than required, bucket overflow can still occur. We handle bucket overflow by using **overflow buckets**. If a record must be inserted into a bucket *b*, and *b* is already full, the system provides an overflow bucket for *b*, and inserts the record into the overflow bucket. If the overflow bucket is also full, the system provides another overflow bucket, and so on. All the overflow buckets of a given bucket are chained together in a linked list, as in Figure 11.24. Overflow handling using such a linked list is called **overflow chaining**.==

==We must change the lookup algorithm slightly to handle overflow chaining. As before, the system uses the hash function on the search key to identify a bucket *b*. The system must examine all the records in bucket *b* to see whether they match the search key, as before. In addition, if bucket *b* has overflow buckets, the system must examine the records in all the overflow buckets also.==

The form of hash structure that we have just described is sometimes referred to as **[[Closed Hashing|closed hashing]]**. ==Under an alternative approach, called **[[Open Hashing|open hashing]]**, the set of buckets is fixed, and there are no overflow chains.== ==Instead, if a bucket is full, the system inserts records in some other bucket in the initial set of buckets *B*. One policy is to use the next bucket (in cyclic order) that has space; this policy is called *linear probing*. Other policies, such as computing further hash functions, are also used.== ==Open hashing has been used to construct symbol tables for compilers and assemblers, but closed hashing is preferable for database systems.== ==The reason is that deletion under open hashing is troublesome. Usually, compilers and assemblers perform only lookup and insertion operations on their symbol tables. However, in a database system, it is important to be able to handle deletion as well as insertion. Thus, open hashing is of only minor importance in database implementation.==

---
#### Explained: Closed hashing vs Open hashing
Closed hashing and open hashing are two different collisions resolution techniques used in hash tables. Here's how the differ:
1. **Closed Hashing (or Closed Addressing):**
	- In closed hashing, also known as closed addressing, collisions are resolved by storing all colliding elements in the same bucket or slot of the hash table.
	- Each bucket in the hash table typically contains a linked list or another data structure to hold multiple elements that hash to the same index.
	- When inserting a new element that hashes to a bucket already occupied by other elements, the new element is appended to the existing element in the bucket.
	- Retrieval involves computing the hash of the search key to determine the bucket, and then searching the elements within that bucket for the desired key.
	- Common techniques for handling deletions involve marking deleted elements or using special markers to distinguish them from active elements.
2. **Open Hashing (or Open Addressing):**
	- In open hashing, also known as open addressing, collisions are resolved by storing colliding elements in other locations within the hash table.
	- When a collision occurs, the hash function is applied iteratively to compute alternative hash codes until an empty slot (or a slot marked for deletion) is found.
	- Common strategies for finding alternative slots include linear probing (checking consecutive slots), quadratic probing (using a quadratic function to compute offsets), or double hashing (applying a secondary hash function).
	- Insertion involves finding an empty slot using the collision resolution strategy and placing the elements there.
	- Retrieval requires following the same probing sequence to locate the element or determining that it does not exist in the table.
	- Deletion typically involves marking the slot deleted rather than physically removing the element to avoid breaking the probing sequence.

In summary, the main difference between closed hashing and open hashing lies in how they handle collisions:

- **Closed Hashing:** Collisions are resolved by storing colliding elements together in the same bucket of the hash table.
- **Open Hashing:** Collisions are resolved by finding alternative slots within the hash table to store colliding elements.

Open hashing is often preferred in scenarios where insertions are more frequent than deletions, and deletions are not frequent or are not a primary concern. Here are some reasons why open hashing may be favored in such situations:

1. **Space Efficiency:**
	- Open hashing typically requires less memory overhead compared to closed hashing because it doesn't need to maintain additional data structures (like linked lists) within each bucket to handle collisions.
	- Without the need for separate data structures in each bucket, open hashing can be more memory-efficient, especially when the load factor (the ration of elements to slots) is low.
2. **Cache-Friendly:**
	- Open hashing can be more cache-friendly, especially in scenarios where the hash table fits entirely or largely in the CPU cache.
	- Accessing consecutive memory locations during probing (as in linear probing) can result in better cache utilization and faster retrieval times compared to traversing linked lists in closed hashing.
3. **Simplicity and Speed:**
	- Open hashing algorithms are often simpler to implement compared to closed hashing, which involves managing linked lists or other data structures for collision resolution.
	- The absence of linked lists or other structures means that insertion and lookups can be faster in open hashing, especially when the table is small and collisions are infrequent.
4. **Predictability:**
	- With open hashing, the position of an element in the hash table is determined solely by its hash value and the probing sequence, making the behavior more predictable.
	- This predictability can simplify the implementation and analysis of hashing algorithms, as well as make debugging and testing easier.
5. **Suitability for Some Applications:**
	- In certain applications or environments where deletions are rare or not a primary concern, the simplicity and efficiency of open hashing may be preferable.
	- For example, in some embedded systems or real-time applications where memory and processing resources are limited, open hashing may offer a practical and efficient solution for handling insertions and lookups.

In summary, open hashing is often chosen when insertions are frequent, deletions are infrequent, and the focus is on simplicity, speed, and memory efficiency. However, it's essential to consider the specific requirements and characteristics of the applications or system when selecting a collision resolution technique for hash tables.

The preference for infrequent deletions in the context of open hashing is mainly due to the challenges and complexities associated with handling deletions in open addressing-based collision resolution techniques. Here's why infrequent deletions are desirable for open hashing:

1. **Clustered Deletions:**
	- In open addressing, when an element is deleted from a slot, it typically leaves behind a tombstone or a special marker to indicate that the slot is now vacant.
	- If deletions occur frequently, especially in clusters or consecutive slots, it can lead to the formation of large clusters of tombstones, known as tombstone clustering.
	- Tombstone clustering can significantly degrade the performance of open hashing by increasing the length of probing sequences and slowing down retrieval operations.
2. **Probing Sequence Disruptions:**
	- Deletions in open addressing can disrupt the probing sequence used to locate elements in the hash table.
	- When a slot is marked as deleted, subsequent probes may need to skip over these deleted slots to find the next active elements, which adds complexity and overhead to retrieval operations.
	- Frequent deletions can fragment the hash table and disrupt the probing sequence, making it less predictable and potentially reduce the efficiency of lookup operations.
3. **Efficiency Concerns:**
	- Handling deletions in open addressing often requires additional bookkeeping to manage tombstones and maintain the integrity of the probing sequence.
	- If deletions are frequent, the overhead associated with managing tombstones and maintaining probing sequences becomes significant, potentially negating the advantages of open hashing in terms of simplicity and speed.
4. **Memory Utilization:**
	- Frequent deletions combined with tombstone clustering can lead to inefficient memory utilization in the hash table.
	- Tombstones occupy space in the hash table but do not contribute to the actual data stored, leading to wasted memory.
	- Over time, if deletions are frequent, the hash table may become fragmented and less space-efficient, impacting overall performance.

In summary, while open hashing offers advantages in terms of simplicity, speed, and memory efficiency, frequent deletions can introduce complexities and inefficiencies, particularly in managing tombstones and maintaining probing sequences. Therefore, open hashing is typically preferred in scenarios where deletions are infrequent or not a primary concern, allowing the benefits of open addressing to be fully realized without significant overhead.

---
==An important drawback to the form of hashing that we have described is that we must choose the hash function when we implement the system, and it cannot be changed easily thereafter if the file being indexed grows or shrinks. Since the function *h* maps search-key values to a fixed set *B* of bucket addresses, we waste space if *B* is made large to handle future growth of the file. If *B* is too small, the bucket contains records of many different search-key values, and bucket overflows can occur. As the file grows, performance suffers.== 
## Hash Indices
Hashing can be used not only for file organization, but also for index-structure creation. A **hash index** organizes the search keys, with their associated pointers, into a hash file structure. We construct a hash index as follows. We apply a hash function on a search key to identify a bucket, and store the key and its associated pointers in the bucket (or in overflow buckets). Figure 11.25 shows a secondary hash index on the *instructor* file, for the search key *ID*. The hash function in the figure computes the sum of the digits of the *ID* modulo 8. The hash index has eight buckets, each of size 2 (realistic indices would, of course, have much larger bucket sizes). One of the buckets has three keys mapped to it, so it has an overflow bucket. In this example, *ID* is a primary key for *instructor*, so each search key has only one associated pointer. In general, multiple pointers can be associated with each key.

![[Pasted image 20240506231023.png]]

We use the term *hash index* to denote hash file structures as well as secondary hash indices. Strictly speaking, hash indices are only secondary index structures. A hash index is never needed as a clustering index structure, since, if a file itself is organized by hashing, there is no need for a separate hash index structure on it. However, since hash file organization provides the same direct access to records that indexing provides, we pretend that a file organization by hashing also has a clustering hash index on it.
## Related Articles
- [[Chapter 11 - Indexing and Hashing]]