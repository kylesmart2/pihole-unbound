# If you are wanting to do this on a raspberry pi, these steps will help you get there a lot faster.

Just follow these simple commands to install docker. (from dockers website: )

curl -fsSL https://get.docker.com -o get-docker.sh 
sudo sh get-docker.sh 
sudo usermod -aG docker <your-user>     #make sure you remove <your-user> and put your username in place i.e. pi 
sudo reboot
  
# Install Portainer
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

Go to the ip address of your pi on a different machine (command: ip add) to find your ip address if unsure
i.e. 192.168.1.4:9000

It will ask you to enter a username and password, default username is admin. The password has to be at least 
12 characters.

Once you set the password, make sure you click local for the docker environment you are monitoring.
After that you can click local again and you will have the full portainer menu

1. On the left hand side click Stacks
2. Click Add stack
3. Name your stack. i.e pihole
4. Paste the docker-compose2.yml file contents into web editor.
5. Make sure you change the Server IP to your Raspberry Pi's ip address
6. Change the timezone if necessary. https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List
7. Pihole best performs using wired ethernet, if you are using wireless, make sure you change eth0 to wlan0
8. You can enable access control, but if you're new or the only one controling it, you can just turn it off.
9. Click Deploy the stack. This will take a few minutes depending on your internet connection.
10. Once it is finshed it will let you know.
11. Click on the Containers tab.
12. Find your pihole container and click the >_ symbol; this will take you to the console. 
13. Click connect.
14. Once connected, type this command followed by a space and then your password:
    pihole -a -p <password>
15. Open a new tab on your browser and enter your pi's ip address. (I.e. http://192.168.1.4/admin)
16. Login with the password you just created.
17. If you wish to get hostname's for your devices from your router, you need to go to the Settings
18. Click the DNS tab and scroll down to the very bottom where you will find Use Conditional Formatting
19. Check the box and enter your networks CIDR notation (i.e. 192.168.1.0/24) and then your routers IP address 
   (i.e. 192.168.1.1) then click save.
20. Congratulations, you now have Pi-hole running as a resursive DNS server on your raspberry pi. Now you just 
    need to add your pi's ip address in your routers setup to the DNS servers to be handed out to your devices. 

# To update pihole when it says at the bottom of the webpage that an update is available, just open Portainer  
  in a browser window and click Images.
1. Click in image and type in pihole/pihole:latest
2. Pull the image.
3. After the image has been pulled, click on Stacks.
4. Click on your pihole stack.
5. Click editor.
6. Click update stack. 
7. Now you should have the newest version of Pihole running and all of your settings should remain the same. 



