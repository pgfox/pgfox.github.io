# Authelia

Authelia is an open source authentication and authorisation server. I use it for authenticating users in my docker compose network. Caddy (the gateway) uses Authelia for ForwardAuth. It also acts as an OIDC provider for services configured to use OIDC. 

It stores the actual users info in LLDAP.


## why use Authelia

- light weight (small footprint for VPS/Cloud machine)
- uses yaml file based configuration (handy for version control)
- plays well with Caddy and lldap 
- docker friendly (one caontainer is enough)

## Limitations

- Sessions live in memory. Using my configuration - the Authelia container is restart everyone has to log back in. I could add Redis layer but for my requirements it is acceptable to log out. 

- One factor authentication. I am only configuring Authelia to use one factor authenticaton. 2FA seemed a little overkill for now. But 2FA options are configurable.


