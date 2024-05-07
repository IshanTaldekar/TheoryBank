---
tags:
  - interesting
---
## Overview
==Many queries references only a small proportion of the records in a file.== For example, a query like "Find all instructors in the Physics department" or "Find the total number of credits earned by the student with *ID* 22201" references only a fraction of the student records. ==It is inefficient for the system to read every tuple in the *instructor* relation to check if the *dept_name* value is "Physics". Likewise, it is inefficient to read the entire *student* relation just to find the one tuple for the *ID* "32556".== Ideally, the system should be able to locate these records directly. To allow these forms of access, we design additional structures that we associate with files.
## Basic Concepts
An index for a file in a database system works in much the same way as the index in this textbook. If we want to learn about a particular topic (specified by a word or a phrase) in this textbook, we can search for the topic in the index at the back of the book, find the pages where it occurs, and then read the pages to find the information for which we are looking. The words in the index are in sorted order, making it easy to find the word we want. Moreover, the index is much smaller than the book, further reducing the effort needed.

Database-system indices play the same role as book indices in libraries. For example, ==to retrieve a *student* record given an *ID*, the database system would look up an index to find on which disk block the corresponding record resides, and then fetch the disk block, to get the appropriate *student* record.==

==Keeping a sorted list of students' *ID* would not work well on very large databases with thousands of students, since the index would itself be very big, further even though keeping the index sorted reduces the search time, finding a student can still be rather time-consuming.== Instead, more sophisticated indexing techniques may be used. 

---
#### Explained
- **Analogy with book indices:**
	- **Book index analogy:** In a textbook, the index at the back of the book serves as a reference tool to quickly locate information about specific topics. It lists key terms or topic alphabetically along with the page numbers where they can be found in the book's content.
	- **Database index analogy:** Similarly, in a database, an index serves as a reference structure to quickly locate specific data records within a large dataset. It provides a mapping between search keys (e.g., student IDs) and the locations (e.g., disk blocks) where the corresponding records are stored.
- **Efficiency of indices:**
	- **Book index efficiency:** Searching for information in a textbook using the index is efficient because the index is sorted alphabetically, making it easy to locate the desired term quickly. Additionally, the index is much smaller than the entire book, reducing the effort needed for searching.
	- **Database index efficiency:** Similarly, database indices are designed to facilitate efficient data retrieval. By maintaining sorted list of search keys (e.g., student IDs), database systems can quickly locate the desired records without having to scan through the entire dataset. This reduces the time and effort needed for data retrieval operations.
- **Challenges with large datasets:**
	- **Book index challenge:** In very large textbooks, the index itself may become large, making it less efficient to use. However, the index is still valuable for quickly locating information within the book.
	- **Database index challenge:** Similarly, in very large databases with thousands or millions of records, maintaining a sorted index of search keys (e.g., student IDs) can become impractical due to the size of the index. Additionally, even with a sorted index, searching for a specific record may still be time-consuming, especially if the database is extremely large.

---

There are two basic kinds of indices:

- **Ordered indices.** Based on a sorted ordering of the values.
- **Hash indices.** Based on a uniform distribution of values across a range of buckets. The bucket to which a value is assigned is determined by a function, called a *hash function*.

We shall consider several techniques for both ordered indexing and hashing. No one technique is the best. Rather, each technique is best suited to particular database applications. Each technique must be evaluated on the basis of these factors:

- **Access types:** The types of access that are supported efficiently. Access types can include finding records with a specified attribute value and finding records whose attribute values fall in a specified range.
- **Access times:** The time it takes to find a particular data item, or set of items, using the technique in question.
- **Insertion time:** The time it takes to insert a new data item. The value includes the time it takes to find the correct place to insert the new data item, as well as the time it takes to update the index structure.
- **Deletion time:** The time it takes to delete a data item. This value includes the time it takes to find the item to be deleted, as well as the time it takes to update the index structure.
- **Space overhead:** The additional space occupied by an index structure. Provided that the amount of additional space is moderate, it is usually worthwhile to sacrifice the space to achieve improved performance.

We often want to have more than one index for a file. For example, we may wish to search for a book by author, by subject, or by title.

==An attribute or set of attributes used to look up records in a file is called a **search key**. Note that this definition of *key* differs from that used in *primary key*, *candidate key*, and *superkey*. This duplicate meaning for *key* is (unfortunately) well established in practice. Using our notion of a search key, we see that if there are several indices on a file, there are several search keys.==
## Related Articles
- [[Chapter 11 - Indexing and Hashing]]
