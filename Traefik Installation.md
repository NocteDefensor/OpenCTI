# Installing Traefik v2 Reverse Proxy
---

## Purpose

- The purpose of Traefik is to act as a reverse proxy to for the docker containers. Using Traefik, enables you to easily incorporate TLS certificates via let's encrypt along side DNS challenge for domain/IP ownership verification.
## Installation

1. **Disclaimers**
    - This guide and corresponding docker-compose file was written for use with a domain using namecheap as the Domain registrar and DNS provider. Many people prefer use of cloudflare to incorprate their anti-DDOS and WAF capabilities.  All that will change, is the environmental variables and a few lines in the docker-compose.yml file within portainer. You can find guidance for these specific changes here: https://doc.traefik.io/traefik/https/acme/#domain-definition
    - In addition to any enviromental variable additions/changes that must be made, the following line must be changed in the docker-compose file
    `--certificatesResolvers.mytlschallenge.acme.dnsChallenge.provider=namecheap`
        - You will simply exchange 'cloudflare' or whatever DNS provider your domain uses. 
    - In addition to adding any environmental variables within portainer(will show how further in the instructions) you will also need to add the corresponding variable entries in this section within the docker-compose

![image.png](/.attachments/image-172ab0d1-bb08-4b4c-a70b-7db0debfc267.png)
        - For example: 
        `CF_API_EMAIL=${CF_API_EMAIL}`
        `CF_DNS_API_TOKEN=${CF_DNS_API_TOKEN}`

**2. Create an "overlay" network within portainer**
   - Navigate to "networks" within the menu on the left side

   ![image.png](/.attachments/image-51695b62-6e6b-47b9-ab97-40e64c563728.png)

   - Click "Add Network"

   ![image.png](/.attachments/image-b49538d4-6f0b-4a53-8aa7-016dae9f45b7.png)

   - Title the network "proxy" and select "overlay" as the driver.
   - Click "Create the Network" at the bottom of the page

   ![image.png](/.attachments/image-be06ac63-3b09-4a3a-9cfb-bf1ad548ef50.png)

**3. Build a Traefik "stack"**
    - Within your Portainer web GUI, navigate to "stacks" by clicking stacks in the menu on the left.
![Inkedmastadon3_LI.jpg](/.attachments/Inkedmastadon3_LI-f2044121-78bc-49e4-988a-45fb1827796d.jpg)
    
    - Click "Add Stack"
![image.png](/.attachments/image-81b105c7-75bf-45fb-b816-adc98b4b68a4.png)

    - You will then be taken to an in browser editor where you can copy and paste the contents of the "Traefik-docker-compose.yml file located within my git repository [here](https://dev.azure.com/Mastadamus/_git/OpenCTI?path=/Traefik-docker-compose.yml)

    ![image.png](/.attachments/image-b5c5c7b5-0f6a-4bbf-b7ab-f1a4dc234010.png)

      - Make edits to the docker-compose file within Portainer as appropriate
        - Go to the labels section and insert your domain name where ever it says "<ChangeME> EX:
        ` - "traefik.http.routers.api.tls.domains[0].sans=*.<ChangeME>.com"` would become
        ` - "traefik.http.routers.api.tls.domains[0].sans=*.YourDomainName.com"`

    - Add Environmental variables and corresponding values to the "Environment" section below where you pasted the contents of the docker-compose file.
    ![image.png](/.attachments/image-4aacc3ae-9d48-45f3-aeaf-7ef4b696bbf7.png)
        - The picture shows 'Namecheap' but you would substitute whatever your DNS provider mandates per the traefik docs posted earlier
        - The TraefikPassword is to enable basic auth for the traefik management GUI. IF used in production systems, I highly recommend integrating a SSO with MFA. That is beyond the scope of this guide. 
        - That password uses htpasswd format you can create a username:password pair by either using linux CLI like so:
        `echo $(htpasswd -nbB $USER “YourPasswordInHere”)` -Replacing `$USER` with your desired username OR
        - you may use a site that allows you to create these such as https://wtools.io/generate-htpasswd-online
        - You will need to add an variable to this section titled "emailaddress" and set the corresponding value as your email address. 

**4.  Start the Traefik Stack**
   - Prior to starting the Traefik stack, you need to ensure you've created the appropriate DNS A records for your subdomain and change the value 'traefik' in the following lines to match your subdomain:
  `- "traefik.http.routers.api.rule=Host(`traefik.<ChangeME>.com`)"`
  `- "traefik.http.routers.api_http.rule=Host(`traefik.<ChangeME>.com`)"`
    - For instance, If you wish your traefik dashboard to be available at the subdomain "dashboard" for your site, you would make an a record for "dashboard" and change the lines to read `dashboard.YourDomain.com`
  - Creating A records is beyond the scope of this guide. Please refer to your DNS registar specific guidelines. Generally speaking, you will create an A record for the desired subdomain and match it to the public IP of your Main node from which traefik will be stood up on. 
  - Provided your DNS records are created and pointed at your the public IP of your master node, you may now start the stack by clicking "Deploy the Stack"
![image.png](/.attachments/image-30651ecc-65ba-46ae-aab8-e1b5e747ef25.png)

    - Traefik, upon its initial run, will grab certificates from the Acme "staging server" which is for testing purposes. You may check these staging certificates by navigating to the traefik dashboard, entering your username and password in the basic auth "portal" and then checking the certificate out. It should say staging.  After the initial run, we must bring down traefik, delete the "acme.json" file and comment out the following line by placing a # in front of it:
  ` # - --certificatesResolvers.mytlschallenge.acme.caServer=https://acme-staging-v02.api.letsencrypt.org/directory`
        - You may delete acme.json by navigating to "volume" and selecting  the "browse" button near the volume "traefik_letsencrypt". 
          - After clicking browse you will see a file "acme.json" you can rename this to acme.json1 or delete it in its entirety.
![image.png](/.attachments/image-2aa4e09d-b392-42b3-b5a0-b171c861e8de.png)
![image.png](/.attachments/image-8c66c4a1-22df-4e05-9fb2-8934fd71819f.png)

- Restart Traefik after deleting acme.json and commenting out the previously mentioned line.
- Traefik should now complete the DNS challenge and grab a wildcard cert for your domain. 
  - Check this cert by navigating to your traefik dashboard and checking the cert in your browser. 
- Once Traefik has grabbed the wildcard cert we need to prevent traefik from doing this process for every subdomain you use traefik for. To this end we will comment out the following line within the docker compose and restart the stack:
  `#- "traefik.http.routers.api.tls.certresolver=mytlschallenge"`

5. **Add Traefik Labels to Portainer docker-compose to utlize TLS for our Portainer GUI**
    - To accomplish this task we will need to edit the portainer docker-compose using the linux CLI. we cannot manage the docker-compose within portainer as it wasn't initially created with portainer. 
    - run `sudo nano portainer-agent-stack.yml` from within the `/opt/portainer/` directory on your Main node. NOTE - you may use VI/VIM or whatever text editor you wish. 
    - Replace the contents of portainer-agent-stack.yml with the contents of the file "Portainer-with-Traefik.yml" that is located in the repository associated with this project. 
      - Notice that I have removed the port mapping 19000:9000 as that port mapping will be accomplished by the traefik label
    `- "traefik.http.services.portainer.loadbalancer.server.port=9000"`
      - Like the traefik dashboard, ensure you've built the corresponding A records within your DNS provider and map them to the main node public IP of your docker swarm.
      - Change the following lines to match your A records/preferences
      `- "traefik.http.routers.portainer.rule=Host(`portainer.<ChangeMe>.com`)"`
      `- "traefik.http.routers.portainer_http.rule=Host(`portainer.<ChangeMe>.com`)"`
        - You may changed the value "portainer" to whatever subdomain you wish to reach your portainer gui at. EX: `PortainerDash.Example.com` Just make sure it matches your A records you've created. 

6. Restart Portainer
    - Once you've created the A records and replaced the contents of the portainer-agent-stack.yml file with the contents of the Portainer-with-Traefik.yml and made the necessary customizations, you may restart portainer with the following command:
  `docker stack deploy --compose-file=portainer-agent-stack.yml portainer`
        - This will rebuild portainer with the Traefik reverse proxy functionality. You should then be able to reach the portainer GUI at whatever FQDN you set ie. `Https://Portainer.YourDomain.com`
  
  
  
  