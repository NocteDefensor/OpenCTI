# Installing Traefik v2 Reverse Proxy
---

## Purpose

- The purpose of Traefik is to act as a reverse proxy to for the docker containers. Using Traefik, enables you to easily incorporate TLS certificates via let's encrypt along side DNS challenge for domain/IP ownership verification.

## Installation

1. Disclaimers
    - This guide and corresponding docker-compose file was written for use with a domain using namecheap as the Domain registrar and DNS provider. Many people prefer use of cloudflare to incorprate their anti-DDOS and WAF capabilities.  All that will change, is the environmental variables and a few lines in the docker-compose.yml file within portainer. You can find guidance for these specific changes here: https://doc.traefik.io/traefik/https/acme/#domain-definition
    - In addition to any enviromental variable additions/changes that must be made, the following line must be changed in the docker-compose file
    `--certificatesResolvers.mytlschallenge.acme.dnsChallenge.provider=namecheap`
        - You will simply exchange 'cloudflare' or whatever DNS provider your domain uses. 