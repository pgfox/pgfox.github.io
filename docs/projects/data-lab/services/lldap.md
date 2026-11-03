# LLDAP

LLDAP stores the users

In the current setup LLDAP is not strictly necessary. Everything talks to Authelia and Authelia talks to LLDAP. 
I could have used a yaml configuration file in Authelia to store users.

However LLDAP is very lightweight and I wanted to have a seperations of concern )service/one job setup). it may be useful later if a service is introduced that need to talk to LDAP directly.

LLDAP is reachable only in the docker network. 

## Why LLDAP specifically

- lightweight (single Rust binary use SQLite).
- simple WebUI for managing users/groups
- simple schema (full LDAP schema can be complicated)
- plays well with Authelia
 

## Limitation

- No replication/HA - in my setup not needed.

- LDAP schema limited flexibility - again fine in my setup.

- no passord policy (does not enforce rules for passwords like: min length , block old passwords) - in my setup not concerning as users are limited and added by me.
