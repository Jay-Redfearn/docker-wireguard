# DigitalOcean Droplet Installation

First, I created a DigitalOcean account with the promo code given.

I then went into the dashboard to create a droplet. I chose the following configurations:

	“Distribution: Ubuntu 20.04 (LTS) x64
	Plan: Shared CPU - Basic Plan
	CPU: Regular Intel with SSD (1GB, 1CPU; 25GB SSD; 1000 GB transfer)
	Datacenter: New York – 1
	Authentication: SSH keys”

I created a new SSH key by going into my macOS Terminal and typing “ssh-keygen”. I was prompted with a file in which to save the key and I named the file “Ubuntu-SSH”. After, the system asked me to create a passphrase, which I created for the SSH key. Two files, one with no extension and another with a .pub extension were created (Ubuntu-SSH and Ubuntu-SSH.pub, respectively).

I also did “chmod 400 ubuntu-ssh” and “chmod 400 ubuntu-ssh.pub” so that the Terminal will be able to use the keys to log into the droplet. After that was done, I moved the files into the .ssh folder with the “mv” command. I added the ssh keys to the system permanently by typing “ssh-add .ssh/ubuntu-ssh”.

After the files were created, I opened the contents of the .pub file and copied the contents over to the “SSH key content must be a valid SSH key” field under the “Add public SSH key” window in DigitalOcean. I named the public SSH key “Ubuntu-SSH”.

I named the hostname as “ubuntu-redfearn”. I then created the droplet.

To log into the droplet, I typed “ssh root@xxx.xxx.xxx.xxx” where x represents my IP address.


# Docker Installation

Type “sudo apt update” to update the apt repository. Then type “sudo apt install docker.io” to install Docker.

Install Docker Compose by typing…
“sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose”

Test the Docker Compose installation by typing “docker-compose –version”.

Make sure Docker is running by typing “sudo service docker status”. If it is not running, type “sudo service docker start”.


# WireGuard Installation

Type the following commands…

“mkdir -p ~/wireguard | mkdir -p /wireguard/config | vim ~/wireguard/docker-compose.yml”

Paste the following into the .yml file…

	“version: '3.8'
	services:
	  wireguard:
	    container_name: wireguard
	    image: linuxserver/wireguard
	    environment:
	      - PUID=1000
	      - PGID=1000
	      - TZ=Asia/Hong_Kong
	      - SERVERURL=1.2.3.4
	      - SERVERPORT=51820
	      - PEERS=pc1,pc2,phone1
	      - PEERDNS=auto
	      - INTERNAL_SUBNET=10.0.0.0
	    ports:
	      - 51820:51820/udp
	    volumes:
	      - type: bind
		source: ./config/
		target: /config/
	      - type: bind
		source: /lib/modules
		target: /lib/modules
	    restart: always
	    cap_add:
	      - NET_ADMIN
	      - SYS_MODULE
	    sysctls:
	      - net.ipv4.conf.all.src_valid_mark=1”

In the .yml file, change the text next to “SERVERURL=” as your IP address that you used to log into the droplet. Change the text next to “TZ=” to “America/Chicago”.

Make sure you are in the “~/wireguard” folder by using the cd command. Type “docker-compose up -d” to install Wireguard.

Type “docker-compose logs -f wireguard” to get the QR codes for Wireguard. Use the “PEER phone 1” QR code for the mobile device that you use to log into the VPN.

On iPhone or another mobile phone, install the WireGuard app. Open the app and click on the “+” in the top right corner, then click “Create from QR code”. Name the WireGuard configuration and allow the phone to apply the permission to your settings.

Go to ipleak.net in your browser and take a screenshot of the settings with the VPN off. Do the same with the VPN turned on. You should have two different IP addresses, which means Wireguard was installed correctly.

On PC or Mac, download the Wireguard app. To get the .conf file, you will need to type “scp root@xxx.xxx.xxx.xxx:~/wireguard/config/peer_pc1/peer_pc1.conf ~/” where x is the IP address to your droplet. After you downloaded the .conf file, import it into Wireguard and start the VPN.

Go to ipleak.net in your browser and take a screenshot of the settings with the VPN off. Do the same with the VPN turned on. You should have two different IP addresses.


# Screenshots

Screenshots of the WireGuard proof of completion are provided on the main branch.

# Sources

http://www.digitalocean.com

https://stackoverflow.com/questions/3466626/how-to-permanently-add-a-private-key-with-ssh-add-on-ubuntu

https://www.namecheap.com/support/knowledgebase/article.aspx/9571/89/how-to-download-a-file-via-ssh/

https://docs.digitalocean.com/products/droplets/how-to/connect-with-ssh/openssh/

https://thematrix.dev/setup-wireguard-vpn-server-with-docker/

https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

https://stackoverflow.com/questions/29933918/ssh-key-permissions-0644-for-id-rsa-pub-are-too-open-on-mac#37779390
