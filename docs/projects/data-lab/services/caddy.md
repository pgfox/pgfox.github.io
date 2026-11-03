# Caddy

Caddy service acts as a single gateway into docker compose network.
It provides ForwardAuth for services I have not configured to use OIDC [see SSO ](../sso.md). 

It also handles HTTPS.  

## Why caddy?

- simple to configure
- built in HTTPS support (little configuration)
- very docker friendly

## How requests are routed

This is an example of a 'site block' in Caddy.

```
sample.pgfox.dev {
	
    forward_auth http://authelia:XXXX {
		uri /api/authz/forward-auth
		copy_headers Remote-User Remote-Groups Remote-Name Remote-Email
	}

	reverse_proxy the_service:8088
}
```

Caddy listens to https and any calles to 'sample.pgfox.dev' are routed through this directive chain. 

## TLS and certificates

I use DNS-01 challenge in both dev and prod infrastructure. I needed to use it in dev as I do not expose my homelab to incoming http. I decided to keep this approach for prod to try to keep prod and dev config alligned where possible.

### DNS-01

The SSL certificate authority (Let's Emcrypt common example) needs to check I own the domain before giving me an SSL certificate. 

Instead of Let's Encrypt calling my server (which is cannot do in my dev environment) it uses the DNS registry to 'comunicate'.  

- My server wants a certificate and calls Let's Encrypt. 
- Let's encrypt replys with a 'secret value'
- My server writes the code to a DNS entry as a TXT record. (my server needs a DNS API token to do this)
- Let's Encrypt check the DNS for the entry

Why is is useful? No open ports  ( like  port 80) for SSL certification proof! 


