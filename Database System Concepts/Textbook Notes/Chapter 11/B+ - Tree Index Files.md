---
tags:
  - interesting
  - low-level
  - b-plus-trees
---
==The main disadvantage of index-sequential file organization is that performance degrades as the file grows, both for index lookups and for sequential scans through the data. Although this degradation can be remedied by reorganization of the file, frequent reorganizations are undesirable.==

---
#### Explained
As the file grows in index-sequential file organization, several factors contribute to the degradation of performance:

1. **Index Lookups:**
	- With a larger file, the index itself grows larger as well. This means that index lookups, which are used to locate specific records based on search keys, become slower.
	- As index size increases, more disk I/O operations are required to search for and retrieve records, leading to longer access times.
	- Additionally, larger indices may exceed the available memory capacity, resulting in increased reliance on disk access, which is slower compared to accessing data from memory.
2. **Sequential Scans:**
	- Sequential scans involve reading data records from the file in sequential order, such as during batch processing or data analysis tasks.
	- As the file grows, the number of records that need to be scanned sequentially increases, resulting in longer scan times.
	- Larger files may also lead to increased disk fragmentation, where data blocks are scattered across the disk, further slowing down sequential scans due to additional seek time.

To remedy the performance degradation associated with a growing file, reorganization of the file is typically performed. Reorganization involves restructuring the file and its associated indices to improve access efficiency. Here's how reorganization can help:

1. **Index Optimization:**
	- Reorganizing the index involves optimizing its structure to reduce access times. This may include rebuilding or reordering the index to improve search efficiency. 
	- Techniques such as index compaction, where empty or redundant index entries are removed, can help reduce the size of the index and improve lookup performance.
2. **Data Reordering:**
	- Reorganization may also involve reordering data records within the file to improve sequential access performance.
	- For example, clustering related records together or grouping records with similar search keys can reduce the number of disk seeks required during sequential scans.
3. **Defragmentation:**
	- Defragmentation processes can be employed to reduce disk fragmentation by rearranging data blocks on the disk to optimize contiguous allocation.
	- Defragmentation helps improve disk access times during both index lookups and sequential scans by minimizing the need for disk head movement.

While reorganization can mitigate the performance degradation associated with file growth, frequent reorganizations are undesirable due to to the overhead involved in restructuring large files and indices. Therefore, strategies such as proactive maintenance and periodic optimization are often employed to keep performance degradation in check while minimizing the need for frequent reorganization.

---
==The $B^+-tree$ index structure is the most widely used of several index structure that maintain their efficiency despite insertion and deletion of data.== ==A $B^+-tree$ index takes the form of a **balanced tree** in which every path from the root of the tree to a leaf of the tree is of the same length. Each non-leaf node in the tree has between $\lceil \frac{n}{2} \rceil$ and *n* children, where *n* is fixed for a particular tree.==

We shall see that the $B^+-tree$ structure imposes performance overhead on insertion and deletion, and adds space overhead. The overhead is acceptable even for frequently modified files, since the cost of file reorganization is avoided. Furthermore, since nodes may be as much as half empty (if they have the minimum number of children), there is some wasted space. This space overhead, too, is acceptable given the performance benefits of the $B^+-tree$ structure.
## Structure of a $B^+-Tree$
==A $B^+-tree$ index is a multi-level index, but it has a structure that differs from that of the multi-level index-sequential file.== Figure 11.7 shows a typical node of a $B^+-tree$. ==It contains up to *n - 1* search-key values $K_1,\ K_2,\ ...,\ K_{n - 1}$, and *n* pointers $P_1,\ P_2,\ ...,\ P_n$. The search-key values within a node are kept in sorted order; thus, if *i < j*, then $K_i < K_j$.==

![[Pasted image 20240505122618.png]]

We consider first the structure of the **leaf nodes**. For *i = 1, 2, ..., n - 1*, pointer $P_i$ points to a file record with search-key values $K_i$. Pointer $P_n$ has a special purpose that we shall discuss shortly.

![[Pasted image 20240505123327.png]]

Figure 11.8 shows one leaf node of a $B^+-tree$ for the *instructor* file, in which we have chosen *n* to be 4, and the search key is *name*.

Now that we have seen the structure of a leaf node, let us consider how search-key values are assigned to particular nodes. ==Each leaf can hold up to *n - 1* values. We allow leaf nodes to contain as few as $\lceil (n - 1) / 2 \rceil$ values. With *n = 4* in our example $B^+-tree$, each leaf must contain at least 2 values, and at most 3 values.==

The range of values in each leaf do not overlap, except if there are duplicate search-key values, in which case a value may be present in more than one leaf. Specifically, if $L_i$ and $L_j$ are leaf nodes and *i < j*, then every search-key value in $L_i$ is less than or equal to every search-key value in $L_j$. If the $B^+-tree$ index is used as a dense index  (as is usually the case) every search-key value must appear in some lead node.

---
#### Explained: "the range of values in each leaf do not overlap"
The statement "the range of values in each leaf do not overlap" refers to a property of $B^+-tree$ leaf nodes where the search-key values contained within a particular leaf node do not overlap with the search-key values in other leaf nodes at the same level of the tree.

Here's a breakdown of what this means:

1. **Distinct Ranges:** Each leaf node in a $B^+-tree$ contains a distinct range of search-key values.
2. **Ordered Structure:** Within each leaf node, the search-key values are kept in sorted order. This means that if you traverse from one end of the leaf node to the other, you encounter the search-key values in ascending or descending order, depending on the sorting criterion.
3. **Non-overlapping Ranges:** When you consider two adjacent leaf nodes in the $B^+-tree$, the range of search-key values in one leaf node does not overlap with the range of search-key values in the other leaf node. In other words, if a search-key value belongs to one leaf node, it does not belong to any other leaf node at the same level.
4. **Efficient Search:** This property ensures that each leaf node covers a unique and continuous range of search-key values. As a result, when searching for a specific search-key value or range of values, the $B^+-tree's$ structure allows for efficient navigation through the tree to find the appropriate lead node(s) containing the desired values.

There can be exceptions when duplicate search-key values are present. Here's a breakdown of what this means:

1. **Distinct Ranges for Unique Values:** In a typical scenario, each leaf node contains a distinct range of search-key values, and no two leaf nodes at the same level share the same search-key value.
2. **Handling Duplicate Values:** However, if there are duplicate search-key values in the dataset, those values may appear in multiple leaf nodes. In this case, the range of values in each leaf node can overlap because multiple leaf nodes may contain the same search-key value.
3. **Duplicates and Data Redundancy:** Duplicate search-key values can lead to redundancy in the index structure, as the same value may need to be stored in multiple leaf nodes. This redundancy is necessary to maintain the integrity of the index while accommodating duplicate values.
4. **Impact on Search Operations:** When searching for a specific search-key value that has duplicates, the search operation may need to traverse multiple leaf nodes to locate all occurrences of the value.

In summary, the statement acknowledges the possibility of overlapping ranges in leaf nodes of a $B^+-tree$ when duplicate search-key values are present. While the general principle is to maintain distinct ranges, the presence of duplicates requires exceptions to this rule to accommodate all occurrences of the duplicate values in the index structure.

---
==Now we can explain the use of the pointer $P_n$. Since there is a linear order on the leaves based on the search-key values that they contain, we use $P_n$ to chain together the leaf nodes in search-key order. This ordering allows for efficient sequential processing of the file.==

==The **non-leaf nodes** of the $B^+-tree$ form a multi-level (sparse) index on the leaf nodes. The structure of non-leaf nodes is the same as that for leaf nodes, except that all pointers are pointers to tree nodes. A non-leaf node may hold up to *n* pointers, and *must* hold at least $\lceil n/2 \rceil$ pointers. The number of pointers in a node is called the *fanout* of the node. Non-leaf nodes are also referred to as **internal nodes**.==

==Let us consider a node containing *m* pointers $(m \le n)$. For *i = 2, 3, ..., m - 1*, pointer $P_i$ points to the subtree that contains search-key values less than $K_i$ and greater than equal to $K_{i - 1}$. Pointer $P_m$ points to the part of the subtree that contains those key values greater than or equal to $K_{m - 1}$, and pointer $P_1$ points to the part of the subtree that contains those search-key values less than $K_1$.==

==Unlike other non-leaf nodes, the root node can hold fewer than $\lceil n/2 \rceil$ pointers; however, it must hold at least two pointers, unless the tree consists of only one node. It is always possible to construct a $B^+-tree$, for an *n*, that satisfies the preceding requirements.==

![[Pasted image 20240505124010.png]]

Figure 11.9 shows a complete $B^+-tree$ for the *instructor* file (with *n = 4*). We have shown instructor names abbreviated to 3 characters in order to depict the tree clearly; in reality, the tree nodes would contain the full names. We have also omitted null pointers for simplicity; any pointer field in the figure that does not have an arrow is understood to have a null value.

![[Pasted image 20240505124315.png]]

Figure 11.10 shows another $B^+-tree$ for the *instructor* file, this time with *n = 6*. As before, we have abbreviated instructor names only for clarity of presentation. Observe that the height of this tree is less than that of the previous tree, which had *n = 4*.

These examples of $B^+-trees$ are all balanced. That is, ==the length of every path from the root to a leaf node is the same. This property is a requirement for a $B^+-tree$.== Indeed, the "B" in $B^+-tree$  stands for "balanced". It is the balance property of $B+-trees$ that ensures good performance for lookup, insertion, and deletion.
## Queries on $B^+-Trees$
Let us consider how we process queries on a $B^+-tree$. Suppose that we wish to find records with a search-key value of *V*. Figure 11.11 presents pseudocode for a function `find()` to carry out this task.

![[Pasted image 20240505134522.png]]

==Intuitively, the function starts at the root of the tree, and traverses the tree down until it reaches a leaf node that would contain the specified values if it exists in the tree.== ==Specifically, starting with the root as the current node, the function repeats the following steps until a leaf node is reached. First, the current node is examined, looking for the smallest *i* such that search-key value $K_i$ is greater than or equal to *V*. Suppose such a value is found; then, if $K_i$ is greater than or equal to *V*, the current node is set to the node pointed to by $P_{i + 1}$,  otherwise $V < K_i$, and then the current node is set to the node pointed to by $P_i$. If no such value $K_i$ is found, then clearly $V > K_{m-1}$, where $P_m$ is the last non-null pointer in the node. In this case the current node is set to that pointed to by $P_m$. The above procedure is repeated, traversing down the tree until a leaf node is reached.==

==At the leaf node, if there is a search-key value equal to *V*, let $K_i$ be the first such value; pointer $P_i$ directs us to a record with search-key value $K_i$. The function then returns the lead node *L* and the index *i*. if no search-key with value *V* is found in the leaf node, no record with key value *V* exists in the relation, and function `find` returns null, to indicate failure.==

==If there is at most one record with a search key value *V* (for example, if the index is on a primary key) the procedure that calls the `find` function simply uses the pointer  $L.P_i$ to retrieve the record and is done. However, in case there may be more than one matching record, the remaining records also need to be fetched.==

==Procedure `printAll` shown in Figure 11.11 shows how to fetch all records with a specified search key *V*.== ==The procedure first steps through the remaining keys in the node *L*, to find other records with the search-key value *V*. If node *L* contains at least one search-key value greater than *V*, then there are no more records matching *V*. Otherwise, the next leaf, pointed to by $P_n$ may contain further entries for *V*. The node pointed to by $P_n$ must then be searched to find further records with search-key value *V*. If the highest search-key value in the node pointed to by $P_n$ is also *V*, further leaves may have to be traversed, in order to find all matching records. The **repeat** loop in `printAll` carries out the task of traversing leaf nodes until all matching records have been found==.

A real implementation would provide a version of `find` supporting an iterator interface similar to that provided by the *JDBC ResultSet*. Such an iterator interface would provide a method `next()`, which can be called repeatedly to fetch successive records with the specified search-key. The `next()` method would step through the entries at the leaf level, in a manner similar to `printAll`, but each call takes only one step, and records where it left off, so that successive calls `next` step through successive records. We omit details for simplicity, and leave the pseudocode for the iterator interface as an exercise for the interested reader.

==$B^+-trees$ can also be used to find all records with search key values in a specified range (*L, U*). For example, with a $B^+-tree$ on attribute *salary* of *instructor*, we find all *instructor* records with salary in a specified range such as (50000, 100000) (in other words, all salaries between 50000 and 100000). Such queries are called **range queries**. To execute such queries, we can create a procedure `printRange(L, U)`, whose body is the same as `printAll` except for these differences: `printRange` calls `find(L)`, instead of `find(V)`, and then steps through records as in procedure `printAll`, but with the stopping condition being that $L.K_i > U$, instead of $L.K_i > V$.==

In processing a query, we traverse a path in the tree from the root to some leaf node. If there are *N* records in the file, the path is no longer $\lceil log_{\lceil n/2 \rceil}(N) \rceil$. 

In practice, only a few nodes need to be accessed. Typically, a node is made to be the same size as a disk block, which is typically 4 kilobytes. With a search-key size of 12 bytes, and a disk-pointer size of 8 bytes, *n* is around 200. Even with a more conservative estimate of 32 bytes for the search-key size, *n* is around 100. With *n = 100*, if we have 1 million search-key values in the file, a lookup requires only $\lceil log_{50}(1,000,000) \rceil$ = 4 nodes to be accessed. Thus, at most four blocks need to be read from disk for the lookup. The root node of the tree is usually heavily accessed and is likely to be in the buffer, so typically only three or fewer blocks need to be read from disk.

An important difference between $B^+-tree$ structures and in-memory tree structures, such as binary trees, is the size of a node, and as a result, the height of the tree. In a binary tree, each node is small, and has at most two pointers. In a $B^+-tree$, each node is large - typically a disk block - and a node can have a large number of pointers. Thus, $B^+-trees$ tend to be fat and short, unlike thin and tall binary trees. In a balanced tree, the path for a lookup can be of length $\lceil log_2 (N) \rceil$, where *N* is the number of records in the file being indexed. With *N = 1,000,000* as in the previous example, a balanced binary tree requires around 20 node accesses. If each node were on a different disk block, 20 block reads would be required to process a lookup, in contrast to the four block reads for the $B^+-tree$. The difference is significant, since each block read could require a disk arm seek, and a block read together with the disk arm seek takes about 10 milliseconds on a typical disk.
## Updates on $B^+-Trees$
When a record is inserted into, or deleted from a relation, indices on the relation must be updated correspondingly. Recall that updates to a record can be modeled as a deletion of the old record followed by insertion of the updated record. Hence we only consider the case of insertion and deletion.

==Insertion and deletion are more complicated than lookup, since it may be necessary to **split** a node that becomes too large as the result of an insertion, or to **coalesce** nodes (that is, combine nodes) if a node becomes too small (fewer than $\lceil n/2 \rceil$ pointers).== Furthermore, when a node is split or a pair of records is combined, we must ensure that balance is preserved. To introduce the idea behind insertion and deletion in a $B^+-tree$, we shall assume temporarily that nodes never become too large or too small. Under this assumption, insertion and deletion are performed as defined next.

- ==**Insertion.** Using the same technique as for lookup from the `find()` function (Figure 11.11), we first find the leaf node in which the search-key value would appear. We then insert an entry (that is, a search-key value and record pointer pairs) in the leaf node, positioning it such that the search key are still in order.==
- ==**Deletion.** Using the same technique as for lookup, we find the leaf node containing the entry to be deleted, by performing a lookup on the search-key value, we search across all entries with the same search-key value until we find the entry that points to the record being deleted. We then remove the entry form the leaf node. All entries in the leaf node that are to the right of the deleted entry are shifted left by one position, so that there are no gaps in the entries after the entry is deleted.==
### Insertion
![[Pasted image 20240505141658.png]]

==We now consider an example of insertion in which a node must be split. Assume that a record is inserted on the instructor relation, with the *name* value being Adams. We then need to insert an entry for "Adams" into the $B^+-tree$ of Figure 11.9. Using the algorithm for lookup, we find that "Adams" should appear in the leaf containing "Brandt", "Califieri", and "Crick". There is no room in this leaf to insert the search-key value "Adams". Therefore, the node is split into two nodes. Figure 11.12 shows the leaf node that results from the split of the leaf nodes on inserting "Adams". The search-key values "Adams" and "Brandt" are in one leaf, and "Califieri" and "Crick" are in the other. In general, we take the *n* search-key values (the *n - 1* values in the leaf node plus the value being inserted), and put the first $\lceil n/2 \rceil$ in the existing node and the remaining values in a newly created node.==

![[Pasted image 20240505141723.png]]

==Having split a leaf node, we must insert the new leaf node into the $B^+-tree$ structure. In our example, the new node has "Califieri" as its smallest search-key value. We need to insert an entry with this search-key value, and a pointer to the new node, into the parent of the leaf node that was split. The $B^+-tree$ of Figure 11.13 shows the result of the insertion. It was possible to perform this insertion with no further node split, because there was room in the parent node for the new entry. If there was no room, the parent would have had to split, requiring an entry to be added to its parent. In the worst case, all nodes along the path to the root must be split. if the root itself is split, the entire tree becomes deeper.==

![[Pasted image 20240505143855.png]]

==Splitting of a non-leaf node is a little different from splitting of a leaf node. Figure 11.14 shows the result of inserting a record with search key "Lamport" into the tree shown in Figure 11.13. The leaf node in which "Lamport" is to be inserted already has entries "Gold", "Katz", and "Kim", and as a result the leaf node has to be split. The new right-hand-side node resulting from the split contains the search-key values "Kim" and "Lamport". An entry (Kim, *n*1) must then be added to the parent node, where *n*1 is a pointer to the new node. However, there is no space in the parent node to add a new entry, and the parent node has to be split. To do so, the parent node is **conceptually** expanded temporarily, the entry added, and the overfull node is then immediately split.==

==When an overfull non-leaf node is split, the child pointers are divided among the original and the newly created nodes; in our example, the original node is left with the first three pointers, and the newly created node to the right gets the remaining two pointers. The search key values are, however, handled a little differently. The search key values that lie between the pointers moved to the right node (in our example, the value "Kim") are move along with the pointers, while those that lie between the pointers that stay on the left (in our example, "Califieri" and "Einstein") remain undisturbed.==

==However, the search key values that lie between the pointers that stay on the left, and the pointers that move to the right is treated differently. In our example, the search key value "Gold" lies between the three pointers that went to the left node, and the two pointers that went to the right node. The value "Gold" is not added to either of the split nodes. Instead, an entry (Gold, *n*2) is added to the parent node, where *n*2 is a pointer to the newly created node that resulted from the split. In this case, the parent node is the root, and it has enough  space for the new entry.==

==The general technique for insertion into a $B^+-tree$ is to determine the leaf node *l* into which insertion must occur. If a split results, insert the new node into the parent of node *l*. If this insertion causes a split, proceed recursively up the tree until either an insertion does not cause a split or a new root is created.==

![[Pasted image 20240505143932.png]]

Figure 11.5 outlines the insertion algorithm in pseudocode. The procedure `insert` inserts a key-value pointer pair into the index, using two subsidiary procedures `insert_in_leaf` and `insert_in_parent`. In the pseudocode, *L, N, P,* and *T* denote pointers to nodes, with *L* being used to denote a leaf node. $L.K_i$ and $L.P_i$ denote the *i*th value and the *i*th pointer in node *L*, respectively; $T.K_i$ and $T.P_i$ are used similarly. The pseudocode also makes use of the function `parent(N)` to find the parent of a node *N*. We compute a list of nodes in the path from the root to the leaf while initially finding the leaf node, and can use it later to find the parent of any node in the path efficiently.

The procedure `insert_in_parent` takes as a parameter *N, K', N'*, where node *N* was split into *N* and *N'*, with *K'* being the least value in *N'*. The procedure modifies the parent of *N* to record the split. The procedure `insert_into_index` and `insert_in_parent` use a temporary area of memory *T* to store the contents of a node being split. The procedures can be modified to copy data from the node being split directly to the newly created node, reducing the time required for copying data. However, the use of the temporary space *T* simplifies the procedures.
### Deletion
![[Pasted image 20240505144933.png]]

==We now consider deletions that cause tree nodes to contain too few pointers. First, let us delete "Srinivasan" from the $B^+-tree$ of Figure 11.13. The resulting $B^+-tree$ appears in Figure 11.16. We now consider how the deletion is performed. We first locate the entry for "Srinivasan" from its leaf node, the node is left with only one entry, "Wu". Since, in our example, *n = 4* and 1 < $\lceil (n - 1) / 2 \rceil$, we must either merge the node with a sibling node, or redistribute the entries between the nodes, to ensure that each node is at least half-full. In our example, the under-full node with the entry for "Wu" can be merged with its left sibling node. We merge the nodes by moving the entries from both the nodes into the left sibling, and deleting the now empty right sibling. Once the node is deleted, we must also delete the entry in the parent node that pointed to the just deleted node.==

==In our example, the entry to be deleted is (Srinivasan, *n*3), where *n*3 is a pointer to the leaf containing "Srinivasan". (In this case the entry to be deleted in the non-leaf node happens to be the same value as that deleted from the leaf; that would not be the case for most deletions.) After deleting the above entry, the parent node, which had a search key value "Srinivasan" and two pointers, now has one pointer (the leftmost pointer in the node) and no search-key values. Since $1 < \lceil n / 2 \rceil$ for *n = 4*, the parent node is under-full. (For larger *n*, a node that becomes under-full would still have some values as well as pointers.)==

==In this case, we look at a sibling node; in our example, the only sibling is the non-leaf node containing the search keys "Califieri", "Einstein", and "Gold". If possible, we try to coalesce the node with its sibling. In this case, coalescing is not possible, since the node and its sibling together have five pointers, against a maximum of four. The solution in this case is to **redistribute** the pointers between the node and its sibling, such that each has at least $\lceil n/2 \rceil = 2$ child pointers. To do so, we move the rightmost pointer from the left sibling (the one pointing to the leaf node  containing "Gold") to the under-full right sibling. However, the under-full right sibling would now have two pointers, namely its leftmost pointer, and the newly moved pointer, with no value separating them. In fact, the value separating them is not present in either of the nodes, but is present in the parent node, between the pointers from the parent to the node and its sibling. In our example, the value "Mozart" separates the two pointers to the node and its sibling after the redistribution. Redistribution of the pointers also means that the value "Mozart" in the parent no longer correctly separates search-key values in the two siblings. In fact, the value that now correctly separates search-key values in the two sibling nodes is the value "Gold", which was in the left sibling before redistribution.== 

==As a result, as can be seen in the $B^+-tree$ in Figure 11.16, after redistribution of pointers between siblings, the value "Gold" has moved up into the parent, while the value that was there earlier, "Mozart", has moved down into the right sibling.==

![[Pasted image 20240505150500.png]]

==We next delete the search-key values "Singh" and "Wu" from the $B^+-tree$ of Figure 11.16. The result is shown in Figure 11.17. The deletion of the first of these values does not make the leaf node under-full, but the deletion of the second value does. It is not possible to merge the under-full node with its sibling, so a redistribution of values is carried out, moving the search-key value "Kim" into the node containing "Mozart", resulting in the tree shown in Figure 11.17. The values separating the two siblings has been updated in the parent, from "Mozart" to "Kim".==

![[Pasted image 20240505150815.png]]

==Now we delete "Gold" from the above tree; the result is shown in Figure 11.18. This results in an under-full leaf, which can now be merged with its sibling. The resultant deletion of an entry from the parent node (the non-leaf node "Kim") makes the parent under-full (it is left with just one pointer). This time around, the parent node can be merged with its sibling. This merge results in the search-key value "Gold" moving down from the parent into the merged node. As a result of this merge, an entry is deleted from its parent, which happens to be the root of the tree. And as a result of that deletion, the root is left with only one child pointer and no search-key value, violating the condition that the root has at least two children. As a result, the root node is deleted and its sole child becomes the root, and the depth of the $B^+-tree$ has been decreased by 1.==

==It is worth noting that, as a result of deletion, a key value that is present in a non-leaf node of the $B^+-tree$ may not be present at any leaf of the tree. For example, in Figure 11.18, the value "Gold" has been deleted from the leaf level, but is still present in a non-leaf node.==

==In general, to delete a value in a $B^+-tree$, we perform a lookup on the value and delete it. If the node is too small, we delete it from its parent. This deletion results in recursive application of the deletion algorithm until the root is reached, a parent remains adequately full after deletion, or redistribution is applied.==

![[Pasted image 20240505151817.png]]

Figure 11.19 outlines the pseudocode for deletion form a $B^+-tree$. The procedure `swap_variables(N, N')` merely swaps the values of the (pointer) variables *N* and *N'*; this swap has no effect on the tree itself. The pseudocode uses the condition "too few pointer/values". For non-leaf nodes, this criterion means less than $\lceil n/2 \rceil$ pointers; for leaf nodes, it means less than $\lceil (n - 1) / 2\rceil$ values. The pseudocode redistributes entries by borrowing a single entry from an adjacent node. We can also redistribute entries by re-partitioning entries equally between the two nodes. The pseudocode refers to deleting an entry (*K*, *P*) from a node. In the case of leaf nodes, the pointer to an entry actually precedes the key value, so the pointer *P* precedes the key value *K*. For non-leaf nodes, *P* follows the key value *K*.
## Non-unique Search Keys
==If a relation can have more than one record containing the same search key value (that is, two or more records can have the same values for the indexed attributes), the search key is said to be a **non-unique search key**.==

==One problem with non-unique search keys is in the efficiency of record deletions. Suppose a particular search-key value occurs a large number of times, and one of the records with the search-key is to be deleted. The deletion may have to search through a number of entries, potentially across multiple leaf nodes, to find the entry corresponding to the particular record being deleted.==

==A simple solution to this problem, used by most database systems, is to make search keys unique by creating a composite search key containing the original search key and another attribute, which together are unique across all records. The extra attribute can be a record-id, which is a pointer to the record, or any other attribute whose value is unique among all records with the same search-key value. The extra attribute is called a **uniquifier** attribute. When a record is to be deleted, the composite search-key value is computed from the record, and then used to look up the index. Since the value is unique, the corresponding leaf-entry can be found with a single traversal from root to leaf, with no further accesses at the leaf level. As a result, record deletion can be done efficiently.==

==A search with the original search-key attribute simply ignores the value of the uniquifier attribute when comparing search-key values.==

==With non-unique search keys, our $B^+-tree$ structure stores each key value as many times as there are records containing that value. An alternative is to store each key value only once in the tree, and to keep bucket (or list) of record pointers with a search-key value, to handle non-unique search keys. This approach is more space efficient since it stores the key value only once; however, it creates several complications when $B^+-trees$ are implemented. If the buckets are kept in the leaf node, extra code is needed to deal with variable-size buckets, and to deal with buckets that grow larger than the size of the leaf node. If the buckets are stored in separate blocks, an extra I/O operation may be required to fetch records. In addition to these problems, the bucket approach also has the problem of inefficiency for record deletion if a search-key value occurs a large number of times.==
## Complexity of $B^+-Tree$ Updates
Although insertion and deletion operations on $B^+-trees$ are complicated, they require relatively few I/O operations, which is an important benefit since I/O operations are expensive. It can be shown that the number of I/O operations needed in the worst case for an insertion is proportional to $log_{\rceil n/2 \lceil}(N)$, where *n* is the maximum number of pointers in a node, and *N* is the number of records in the file being indexed.

The worst-case complexity of the deletion procedure is also proportional to $log_{\rceil n/2 \lceil}(N)$, provided there are no duplicate values for the search key. If there are duplicate values, deletion may have to search across multiple records with the same search-key value to find the correct entry to be deleted, which can be inefficient. However, making the search key unique by adding a uniquifier attribute, as described in Section 11.3.4, ensures the worst-case complexity of deletion is the same even if the original search key is non-unique. 

In other words, the cost of insertion and deletion operations in terms of I/O operations is proportional to the height of the $B^+-tree$, and is therefore low. It is the speed of operation on $B^+-trees$ that makes them a frequently used index structure in database implementations.

In practice, operations on $B^+-trees$ result in fewer I/O operations than the worst-case bounds. With fanout of 100, and assuming accesses to leaf nodes are uniformly distributed, the parent of a leaf node is 100 times more likely to get accessed than the leaf node. Conversely, with the same fanout, the total number of non-leaf nodes in a $B^+-tree$ would be just a little more than 1/100th of the number of leaf nodes. As a result, with memory sizes of several gigabytes being common today, for $B^+-trees$ that are used frequently, even if the relation is very common today, for $B^+-trees$ that are used frequently, even if the relation is very large it is quite likely that most of the non-leaf nodes are already in the database buffer when they are accessed. Thus, typically only one or two I/O operations are required to perform a lookup. For updates, the probability of a node split occurring is correspondingly very small. Depending on the ordering of inserts, with a fanout of 100, only between 1 in 100 to 1 in 50 insertions will result in a node split, requiring more than one block to be written. As a result, on an average an insert will require just a little more than one I/O operation to write updated blocks.

Although $B^+-trees$ only guarantee that nodes will be at least half full, if entries are inserted in random order, nodes can be expected to be more than two-thirds full on average. If entries are inserted in sorted order, on the other hand, nodes will be only half full. 
## Related Articles
- [[Chapter 11 - Indexing and Hashing]]
- [[B+-Tree]]




