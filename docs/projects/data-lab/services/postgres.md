# PostgreSQL

I am using PostgreSQL as a relational database. 


## Why PostgreSQL specifically

I chose PostgreSQL database as it is my preferred open-source DB to use because:

- it is well supported and tends to play nicely with other tools
- it has some useful extensions - vector database (pgvector) and Geospatial 
- great SQL support


## Limitations:

Natively row-oriented - not really intended for columnar data. Although some extensions appear to give some functionality in this direction. 


## User management

PostgreSQL keeps its users/roles within the database. It is not part of the SSO. 

The concept: projects would create the required users/access when the project instantiates its own database.