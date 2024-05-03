- A relational data needs to maintain metadata about the relations which are stored in a structure called the **data dictionary** or **system catalog**. 
	- Among the types of information that the system must store are:
		- Names of relations.
		- Names of the attributes of each relation.
		- Domains and lengths of attributes.
		- Names of views defined on the database, and definition of those views.
		- Integrity constraints (for example, key constraints).
	- Some databases also keep the following data on users of the system:
		- Names of authorized users.
		- Authorization and accounting information about users.
		- Passwords or other information used to authenticate users.
	- Further, the database may store statistical and descriptive data about the relations:
		- Number of tuples in each relation.
		- Method of storage for each relation (for example, clustered or non-clustered)
	- The data dictionary may also note the storage organization (sequential, hash, or heap) of relations, and the location where each relation is stored:
		- If relations are stored in operating system files, the dictionary would note the names of the file (or files) containing each relation.
		- If the database stores all relations in a single file, the dictionary may note the blocks containing records of each relation in a data structure such as a linked list.
	- Supporting Index structures requires storing information about each index on each of the relations:
		- Name of the index.
		- Name of the relation being indexed.
		- Attributes on which the index is defined.
		- Type of index formed.
- All the metadata information constitutes, in effect, a miniature database. Some database systems store such metadata by using special-purpose data structures and code. It is generally preferable to store the data about the database as relations in the database itself, which can simplify the overall structure of the system and harness the full power of the database for fast access to system data.
- The exact choice of how to represent system metadata by relations must be made by the system designers. One possible representation, with primary keys underlined, is shown in Figure 10.16. In this representation, the attribute *index_attributes* of the relation *Index_metadata* is assumed to contain a list of one or more attributes, which can be represented by a character string such as "dept_name, building". The *Index_metadata* relation is thus not in first normal form; it can be normalized, but the above representation is likely to be more efficient to access. The data dictionary is often stored in a non-normalized form to achieve fast access.
  
  ![[Pasted image 20240503122115.png]]
- Whenever the database system needs to retrieve records from a relation, it must first consult the *Relation_metadata* relation to find the location and storage organization of the relation, and then fetch records using this information. However, the storage organization and location of the *Relation_metadata* relation itself must be recorded elsewhere (for example, in the database code itself, or in fixed location in the database), since we need this information to find the contents of *Relation_metadata*.
## Related Articles
- [[Chapter 10 - Storage and File Structure]]


