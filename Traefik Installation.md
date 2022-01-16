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
    - In addition to adding any environmental variables within portainer(will show how further in the instructions) you will also need to add the corresponding variable entries in this section within the docker-compose

![image.png](/.attachments/image-172ab0d1-bb08-4b4c-a70b-7db0debfc267.png)
        - For example: 
        `CF_API_EMAIL=${CF_API_EMAIL}`
        `CF_DNS_API_TOKEN=${CF_DNS_API_TOKEN}`

2. Create an "overlay" network within portainer
   - Navigate to "networks" within the menu on the left side

   ![image.png](/.attachments/image-51695b62-6e6b-47b9-ab97-40e64c563728.png)

   - Click "Add Network"

   ![image.png](/.attachments/image-b49538d4-6f0b-4a53-8aa7-016dae9f45b7.png)

   - Title the network "proxy" and select "overlay" as the driver.
   - Click "Create the Network" at the bottom of the page

   ![image.png](/.attachments/image-be06ac63-3b09-4a3a-9cfb-bf1ad548ef50.png)

3. Build a Traefik "stack"
    - Within your Portainer web GUI, navigate to "stacks" by clicking stacks in the menu on the left.
![Inkedmastadon3_LI.jpg](/.attachments/Inkedmastadon3_LI-f2044121-78bc-49e4-988a-45fb1827796d.jpg)
    
    - Click "Add Stack"
![image.png](/.attachments/image-81b105c7-75bf-45fb-b816-adc98b4b68a4.png)

    - You will then be taken to an in browser editor where you can copy and paste the contents of the "Traefik-docker-compose.yml file located within my git repository [here](https://dev.azure.com/Mastadamus/_git/OpenCTI?path=/Traefik-docker-compose.yml)

    ![image.png](/.attachments/image-b5c5c7b5-0f6a-4bbf-b7ab-f1a4dc234010.png)

    - Add Environmental variables and corresponding values to the "Environment" section below where you pasted the contents of the docker-compose file.
    ![image.png](/.attachments/image-4aacc3ae-9d48-45f3-aeaf-7ef4b696bbf7.png)
        - The picture shows 'Namecheap' but you would substitute whatever your DNS provider mandates per the traefik docs posted earlier
        - The TraefikPassword is to enable basic auth for the traefik management GUI. IF used in production systems, I highly recommend integrating a SSO with MFA. That is beyond the scope of this guide. 
        - That password uses htpasswd format you can create a username:password pair by either using linux CLI like so:
        `echo $(htpasswd -nbB $USER “YourPasswordInHere”)` -Replacing `$USER` with your desired username OR
        - you may navigate to a site that allows you to create these such as https://wtools.io/generate-htpasswd-online
  
  
  