### Characteristics of database approach:
- self describing nature
- program data independence
- support multiple views of data
- multi-user support

### An operation
- has two parts:
	- interface - include operation name and its arguments - like a function call
	- implementation - specify the operation - like the code in a function

### Transaction:
- a process that include one or more operations on a database
- properties:
	- isolation
	- atomicity

### Actors on the scene:
- Database Administrator
- Database Designer - identify the data to be stored and what structure to use
- End users
	- casual users - normal users, user query language, need different data each time
	- naive users - constantly query to database using standard type of queries and updates
	- sophisticated users - interact using database query language or using data analysis software
	- standalone users - 
- system analyst and application programmers - write application programs

### Database Administrator:
- schema definition
- storage structure and access method definition
- schema and physical organization modification
- granting of authorization for data access

### types of data:
- structured data:
	- represented in a strict format
	- all data that can be stored in a row and column manner
- semi-structured data:
	- does not reside in a relational database
	- but has some organizational properties
	- can be processed to be stored in relational database
	- eg: XML
- unstructured data:
	- does not have a predefined data model
	- eg: word, PDF

### Data models:
- used to describe structure of database
- data types, constraints and relations in a database

#### categories of data models
- high level / conceptual data model:
	+ represent data close to how users perceive the data
	+ entity, attributes and relationships are mentioned here
	+ 