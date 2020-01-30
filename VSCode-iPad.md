![Run OCI](../Assets/RUN-OCI-header.png)

# Run VS Code development environment on your iPad!

![VS Code on an iPAD!](/Assets/vscode-ipad.jpg)

Sometimes your travelling and just want to get something done. Sometimes, you only want to take an ipad or sometimes you just want to  want to use a low powered device like a raspberry pi as a temrinal, but you don't want to be constrained by installing packages locally on ARM.

Well as luck would have it there's a quick and free workaround available via Oracle Free Tier, Docker and the open source Code Server!  

---

### Let's get started

The outcomes we're after are:

* A free computer in the sky.
* A full x64 dev enviroment.
* Accessible and ready-to-go from work, home or mobile (tablet).
* VS Code as graphical IDE.

**The pre-requisites are:** 

1) [Oracle Always Free Tier Account.](http://oracle.com/cloud/free)
2) [An SSH key-pair](https://docs.oracle.com/en/cloud/paas/database-dbaas-cloud/csdbi/generate-ssh-key-pair.html#GUID-69EF7E8A-7CD5-482E-A878-882EA21DE2B8)
3) [An hour or so spare :)](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwjR0f67mI7mAhUPfH0KHa95DwIQFjAAegQIARAB&url=https%3A%2F%2Fgettingthingsdone.com%2F&usg=AOvVaw29xE4PaNOode2uC-m_4EyX)

---

## Step 1: Log into your OCI Free Tier

I like to think of VMs here as a couple of free raspberry pi's in the sky.  Great for experimenting with little projects and setting up useful cloud hosted services for yourself!

## Step 2: Create a free VM compute instance

Ok this is where we spin up one of our two always free VMs, however, we'll want the import of our image to be finished, once that's done:

* Navigate to Compute > Instances > Create Instance.
* Give it a catchy name - "OracleCoolDevEnviromement".
* Click "Change Image Source > Ubuntu 18.04 Minimal" Choose the image you just imported.
* The free tier shape should already be selected.
* Clik to assign a public IP
* Copy in your SSH **Public** Key.

![Choose the Image and Shape](../Assets/849E86B3-E207-4480-BC26-C5114F8D0197.png)

## Step 3: Assign a domain / subdomain!

To add secure access via https we're going to assign a subdomain here and use a reverse proxy to route through to the service. Simply put, go to a service like godaddy where you have a 

If we're going to access this outside the cloud console we're going to need a public IP. 

* Navigate to "Instance > Details > Attached VNICs > VNIC Details > IP Addresses".
* From the ... menu click to edit the existing private IP address.
* Select "reserved public IP" and select the IP address you generated in step 3.

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

Now if all that worked you should be able to type the public ip address into your browser and see the log in screen for next cloud. 

However to make this secure we're going to want to add a certificate so we can access this securely over https.

`sudo nextcloud.enable-https lets-encrypt`

Answer the questions, add in your domain and boom! You now have a secure encrypted connection to your nextcloud instance so log in with the admin username and password you provided earlier!

http://oracleisbrill.com
will auto redirect you to https://oracleisbrill.com and you can securely log in!


## Step 6: Log in / Create an SSH tunnel

I've only tried this on mac and linux as that's what's available to me but the idea here is to log into the remote box and create an SSH tunnel which maps our local port 8888 to that of the remote machine. We're going with this extra step so that we can access our Jupyter server through this port mapping. 

Simply fire up a terminal on your local machine with the following command:

`ssh -L :8888:localhost:8888 ubuntu@<ip_address> -i <ssh_private_key_name>)`

The `<ip_address>` is your public IP address which you can record from your instance details.  
The `<ssh_private_key_name>` is the name of the private key on your local machine [see guide if you missed it earlier](https://docs.oracle.com/en/cloud/paas/database-dbaas-cloud/csdbi/generate-ssh-key-pair.html#GUID-69EF7E8A-7CD5-482E-A878-882EA21DE2B8).

## Step 7: Fire up the notebooks and connect

After the terminal connects to the remote box, we've now got a secure connection to the box and can kick off hte pre-installed Jupyter Server.  Simply enter

`jupyter notebook`

This will fire up jupyter with a server on localhost:8888 which we have already conveniently mapped to our own localhost:8888. Did someone say, **Bish, Bash, Bosh?**

## Step 8: Connect to Remote Notebook Server

Click / copy the link in terminal into your browser to log in! 

It will be of the format of...

* http://localhost:8888/?token=biglongtokenoincludingnumbers78708678andletterssfslkfj

This will fire up a jupyter notebook in your local browser, with all code, files and exectuion taking place in the cloud, securely accessed through the SSH tunnel.

Now you're ready to get started!

## Step 9: The Bonus Round!

**VS Code Server** You may want to do some work outside notebooks for which a remote VS Code sever is a good option and is already installed on the server. This [youtube video gives you a good overview of how to set that up](https://www.youtube.com/watch?v=lKXMyln_5q4) and can be configured pretty easily for you're remote instance.

**VNC Access** Sometimes you just want a gui and luckilly we've already pre-configured everything you need here. [See the end of this guide for details on how to connect](https://youtu.be/Kb6v1GqeAc0?t=336)

Bonus points to anyone who follows this though and I'll write up a quick summary of that soon in another article.

## Step 10: Share your experience and your requirements!

The point of this repo is to gather to gether a range of opensource / creative commons guides and images for working with OCI Free Tier (and paid services).

So, we ask one simple question, please share your experience and make some requests for other images you'd love to see pre-configured for deployment!

All material on this site is licensed under a [Creative Commons BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
