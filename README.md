# pihole-unbound
Follow these steps to setup pihole using unbound for a fully recursive DNS server to either run on a Linux computer or server at your house or by using a hosting service online such as Linode.com or Amazon's AWS EC2. This will also run on a Raspberry Pi if you do not wish to use a server. 

This tutorial was primarily setup to give you a step-by-step method of setting up Pihole as a recursive DNS for your use on a server hosted outside of your network. I have outlined the steps that you need to follow in order to set this up on a machine in your home network as well. The internal network doesn't require the extra security steps, so it isn't as invovled. 

This is using UFW as the firewall, but there are other firewalls that can be used on Linux if you choose to use another one. There are plenty of choices, I just chose this one as it was easy and it also came pre-installed on my Debian 10 instance on Linode. 

I have pulled the docker-compose.yml file stuff out of this tutorial and made it a separate file if you wish to use it instead. I found the file myself anyway courtesy of cbcrowe who made the pihole-unbound docker container to make it an easy one step setup to make pihole your fully recursive DNS server. 
# If installing on Raspberry Pi, follow this link to install docker:  https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script

# All of the iptables and ufw firewall settings are only required if you are hosting this server outside of your local network. If you're hosting the server inside your network, please only follow steps 3, 6, 7, 8, 9 (this last step is optional)

1. Install iptables-persistent:	apt install iptables-persistent
   Save tables: iptables-save > /etc/iptables/rules.v4

2. Edit ufw rules, install if not already and enable 

   ufw enable
   
   Rules:
   root@localhost:~# ufw status verbose
   Status: active
   Logging: on (low)
   Default: deny (incoming), allow (outgoing), deny (routed)
   New profiles: skip

   To                         Action      From
   --                         ------      ----
   22                         ALLOW IN    Your outside IP address or internal if self-hosting
   
   9000                       ALLOW IN    IP address
   
   80                         ALLOW IN    IP address
   
   53                         ALLOW IN    IP address
   
   22                         ALLOW IN    VPN IP address. Not necessary if self-hosting
   

   To add rules: ufw allow from "IP address" to any port "port number"
   #Put your Ip address in and choose the port numbers from above.

3. Install docker: follow docker website instructions if needed

4. Edit DOCKER-USER iptables as follows:
   #This is what your IP tables will look like after doing the command at the bottom
   Chain DOCKER-USER (1 references)
   target     prot opt source               destination
   DROP       all  -- !"Your external IP address if hosted outside your network"  anywhere
   RETURN     all  --  anywhere             anywhere

   iptables commmand: 
   iptables -I DOCKER-USER -i "ethernet port"(usually ethh0) ! -s "your IP address" -j DROP

   save iptables:
   iptables-save > /etc/iptables/rules.v4

5. Reboot server

6. Install docker-compose, follow online instructions

7. Create docker-compose.yml file, or clone this repository:
   #if cloning
   mkdir pihole
   cd pihole
   git clone https://github.com/kylesmart2/pihole-unbound.git
   #determine which compose file you are using, if you want to use docker-compose2.yml, rename docker-compose.yml to 
   #unused_docker-compose.yml and rename docker-compose2.yml to docker-compose.yml

   #if creating your own docker-compose file and pasting in the contents
   mkdir pihole
   cd pihole
   nano docker-compose.yml

   Paste the contents of the attached docker-compose.yml file or just download the file and put it on your server. I'd recommend SFTP in terminal or filezilla if you're choosing to download the file and transfer 
   to your server, rather than downloading it to your server directly. 


8. Run docker-compose up -d
   After it is successful, goto server IP address, you should see pihole

   To change pihole password:
   docker exec -it pihole /bin/bash
   After logged in pihole container:
   pihole -a -p "enter your password without quotes"(hit enter)

9. Add a Portainer container. It makes it super easy to update your containers if you setup the docker-compose.yml file as a stack using portainer. Must use version 2.1 (If you're on the current version, 3 works.)

   (Copy entire command and paste into terminal)
   docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

   Navigate to your server's IP address followed by a colon and then 9000
   i.e. 192.168.1.2:9000

   Enter a password for your adminstrator account, click local after intial sign-in to see your local container stack. 


10. Periodically check your Pi-hole admin page at the bottom to check to see if there is an update available. When there is
    an update available, ssh into your server, or pull up the server's terminal and enter these commands:

    cd pihole
    docker compose pull

    Once the download has finised, simply run:
    docker compose up -d

    If the output of that command shows Running 2/1 or Running 2/0, it didn't pull a new version of one or one may not have been available, or neither had an update.

