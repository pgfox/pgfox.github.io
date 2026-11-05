# Identity & Access

Brief overview of how the users are structured/interact. 

## SSO Users
SSO users (e.g used to login to Superset or Grafana) are stored in LLDAP.

LLDAP provides a simple UI to manage users

For SSO details see [SSO](../data-lab/sso.md)

## Superset 

SSO users to login to Superset GUI. 

Some dashboards are public to allow low friction access (from pgfox.dev site)

Postgres DB access to read the data - it uses the POSTGRES_SUPERSET_USER (see postgres section)

## Postgres

Postgres holds its users/roles inside the Database. The project has two categories of postgres users - service accounts and persons:

- service accounts - used by tools to access postgres
- person accounts - real people logging in 


| User | Type | Used by | Access |
| --- | --- | --- | --- |
| `POSTGRES_ADMIN_USER` | Admin | create_db.sh, init.sh | Everything. Created by the infra stack (platform). Only used to set things up: it creates the other users, the databases, the schemas and the grants. |
| `POSTGRES_PIPELINE_USER` | Service account | Loaders, dbt, Prefect flows, and tests | Owns/creates schema raw, stage, core, metadata, mart  |
| `POSTGRES_SUPERSET_USER` | Service account | Superset's database connection | Read-only, and only on mart. Can't see raw, stage, core or metadata, and can't change anything. |

(Person accounts could be created as needed)


## Pgadmin

## SeaweedFS User

## Grafana ??




