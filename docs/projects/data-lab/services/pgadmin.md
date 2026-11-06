# pgAdmin

Pgadmin is available to query postgreSQL.

## Users

SSO user can access the GUI of pgadmin. The connecton created from pgadmin to PostgreSQL must use PostgresSQL role (defined in PostgreSQL)

For SSO, Authelia authentics the user and the user is places in the `Remote-User` header. Caddy addes a Shared Secret before the request goes to pgadmin. If the request has come from Caddy and contains the shared secret than pgadmin trust the `Remote-user`. If the user has not been seen by pgadmin it will automatically crete the user. 

