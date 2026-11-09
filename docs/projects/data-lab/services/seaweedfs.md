# SeaweedFS

## Why SeaweedFS 

I wanted an open source service that would provide an object store (simulate AWS S3). Originally I planned on using MinIO but I discovered that MinIO had archived their open source project. 

SeaweedFS is a popular alternative to MinIO. The docker image for SeaweedFS (chrislusf/seaweedfs) comes with:

- Master component - what volumes holds what data
- Volume Server - stores the bytes
- Filer - folders and filenames
- S3 gateway - S3 compatible API
- Admin UI - web dashboard.


Additional Bonus: Recent version of SeaweedFS include a built in Iceberg. This will allow me to investigate using SeaweedFS combined with Trino/Duckdb as a 'Data Lake House' instead of using Postgres to store the data. For this Phase I am just using it as a Object Store. 

The SeaweedFS S3 gateway allows me to connect with AWS s3 client and use the standard operations like: 

```
aws s3 ls ....
aws s3 sync ....
etc.

```
## security

The SeaweedFS UI doees not support ForwardAuth or OIDC so I need to login with a UID user (managd in SeaweedFS)

For S3 access I use a SECRET KEY ID/ SECRET KEY. Right now I am using a static key.




## How to connect 

...





