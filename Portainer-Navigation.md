# Using Portainer
---

## Navigation

1. Navigate to Portainer by entering `Http://YourIp:19000`
    - Replace "YourIP" with the public IP address of your the Manager node if using a cloud based VM.
    - If the host that you are browsing from is in the same subnet as your docker swarm nodes(i.e you are standing this up on prem or have a vpn tunnel to the cloud subnet) then you can replace YourIP with the private IP of the manager node. 
2. Create a new User.
   - When you first navigate to portainer in your browser it will prompt you to create an user and give it a password. Ensure you give it a complex password as Portainer will have access to many of the environmental secrets such as API's, passwords etc. For production machines considering using a stronger form of authentication such as Auth0 which allows MFA. 
![image.png](/.attachments/image-daba4783-ad33-4d44-8bbe-b2d6e02d0063.png)

3. Post login. 
    - After you create your username and login, you will be presented with a screen like the following:
![InkedMastadon_LI.jpg](/.attachments/InkedMastadon_LI-d59b015f-a559-4a6b-88b9-4932f0225340.jpg)
    - NOTE- You won't see as many stacks,services, or volumes listed under primary as you haven't yet built or deployed any stacks other then the portainer stack. 

    - Click "Primary" you will then be taken to your "dashboard" which will look similar to below but will not contain as many containers, stacks, services, or images etc.
![InkedMastadon1_LI.jpg](/.attachments/InkedMastadon1_LI-2e5f84e9-552d-40ed-9c8c-d68bf6d86f2b.jpg)

    - You are now ready to begin deploying your first stack. I suggest Traefik to allow use of automatic TLS certificate retrieval and reverse proxy capability. 
