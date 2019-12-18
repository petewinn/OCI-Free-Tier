![Run OCI](/Assets/RUN-OCI-header.png)

# Setup your own Personal Cloud Server (files, chat, notes everything!) for free in the cloud!

## Create a Free Tier Instnace

If you don;t already have one, head to [Oracle.com/cloud/free](https://www.oracle.com/cloud/free/) and sign up for a free tier.

"it's like a raspberry pi in the sky!"

Log in > Compute > Instance > Create Instance

Select Ubuntu 18.04 (minimal) as your image.

![Screen1](/Assets/pcs-1.png)

Assign a public IP address and upload your ssh public key (see here for more information)

![Screen2](/Assets/pcs-2.png)

## Base Config and Install

Log into the server via SSH using default username ubuntu

`ssh ubuntu@< public IP > -i < path to your ssh private key >`

Run a quick update of the system.

```
sudo apt update 
sudo apt get upgrade
```

Then it's time to install. We're going to use snap install to keep this simple, but if you want to get your hands dirty with a [full config check out this guide](https://www.linux.com/tutorials/how-install-nextcloud-server-ubuntu/).

`sudo snap install nextcloud`

In the background this snap will setup the rquired apache server, database backend etc to run NextCloud. Perhaps in future we could look at running this with an ATP backend!

## Setup trusted servers to access 

Before we open up any access to this over the web, lets assign an admin account to the server. Run the following command with your own usernamne and password e.g. user = "free@oracle.com" password = "goOracle!"

`sudo nextcloud.manual-install <username> <password>`

Now you'll want to assign trusted severs, so that you can access the server thorugh specified ip address or hostname.

If you're going to use your own domain (required if you want to setup SSL) then you'll want to make sure you have an A record in your DNS point to the IP address for your Oracle Cloud Server.

First check what the trusted domains are setup - it should just be localhost.  Incidentally if you setup an SSH tunnel mapping port 80 to 80 - you'll already be able to log into your nextcloud! Bonus points for doing this now :)

`sudo nextcloud.occ config:system:get trusted_domains`

`sudo nextcloud.occ config:system:set trusted_domains 1 --value=oracleisbrill.com`

*note '1' in this instance means the place inthe list of truste domains so to add another domain - just increment this to '2'*

## The IP Tables issue

This had me tripped up for a while. It appears that out of the box the Oracle Cloud Ubuntu image is configured to block access to ports 80 / 443 in the ip tables config. [This slack overflow was the only reference](https://stackoverflow.com/questions/54794217/opening-port-80-on-oracle-cloud-infrastructure-compute-node) I found for this!

Long story short we want to overtide the defaults (if you know more than I do, you may want to do this differently).

```
sudo iptables -P INPUT ACCEPT
sudo iptables -P OUTPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -F
```

## Setup firewall, port forwarding and SSL

Now we want to install and configure a firewall - for which I'll use `ufw`.

`sudo apt-get install ufw`

sudo ufw status
tells you its turned off right now.

I like to setup the following rules for completeness (I'm a one at a time kinda guy)

```
sudo ufw allow ssh
sudo ufw allow 443/tcp
sudo ufw allow 80/tcp
```

Then turn on that bad boy `sudo ufw enable` it'll warn that this may affect existing SSH connections, but you should be fine so hit a y

Check out your rules with `sudo ufw status numbered`

## Setup port forwarding and SSL

Now we head to the Oracle Cloud Console to set up the port forwarding rules so that traffic can even get to the server in the first place!

1. Menu > Compute > Instances > Select the instnace you created
2. Click on the VCN title from the instance details page
3. Click on "security lists" in the left hand menu
4. Select the default security list
5. Create two new rules for traffic from 0.0.0.0/0 (means anywhere) to port 80 and port 443 respectively.

![Screen3](/Assets/pcs-3.png)

![Screen4](/Assets/pcs-4.png)

![Screen5](/Assets/pcs-5.png)

![Screen6](/Assets/pcs-6.png)

![Screen7](/Assets/pcs-7.png)

Now if all that worked you should be able to type the public ip address into your browser and see the log in scree for next cloud. However to make this secure we're going to want to add a certificate.

`sudo nextcloud.enable-https lets-encrypt`

Answer the questions, add in your domain and boom! You now have a secure encrypted connection to your nextcloud instance so log in with the admin username and password you provided earlier!

http://oracleisbrill.com
will auto redirect you to https://oracleisbrill.com and you can securely log in!
