# A Free (forever) Cloud Based Jupyter data science development environment in 10 steps!

---

Wow, that is not the snappiest title I've ever written! But it is descriptive, so if that's what you're after, hold tight!

Recently I got into a conversation with a data scientist who told me one time consuming part of their job was to paraphrase...

"get people setup with an environment they can play around in and get used to the tools"
This was as simple as a consistent developer environment that could be deployed with a number of useful tools ready to go.

Conversation took the many turns it often does, but an option I thought would be pretty useful would be to leverage Oracle's recently announced "Always Free Tier" to build a training environment that could be given out far and wide and would'n go away after a year!

So I set myself the task seeing if there was an easy way to get this done and get other people to replicate this. The astute amongst you will realise this article is the output of that guide! :)

So the goal was a cloud based development environment which was:

An always on;
Accessible and ready-to-go from work computer, home laptop or a tablet;
Accessible either via terminal or VNC for GUI when required;
Runs Jupyter notebook server to act as a remote data science development / test environment.
and ideally as a bonus runs remote VS Code server for generic development and scripting efforts.
Now the pre-requisites here are: 1) an hour or so 2) an ssh key-pair 3) an Oracle Always Free Tier Account. So grab these, but if you need help on any of that just add a comment below.

Step 1: Log into your OCI Free Tier

Step 2: Import a Dev Image

I created an empty Ubuntu 18.04 image which comes pre-loaded with a few useful tools for VNC / desktop access which isn't a bad start. So let's import that.

Import a custom image (Menu > Compute > Custom Images).
Click import and import a linux image from this URL of image type OCI.
Paste: https://objectstorage.ap-sydney-1.oraclecloud.com/p/Ou2S0FX8KO8Ss-xQDsVAexnD8FTnRYYXebEqhREzuFM/n/sde81zai9pvf/b/HI_Bucket/o/DevBuntsImage
This will take a little while to pull the image, so in the meantime we can do something else.

Step 3: Setup a public IP

When you've got you're environment up and running you'll need to we're going to access from the internet via SSH so it'll need a public IP so let's grab one of those.

Navigate to Menu > Networking > Public IPs.
Create a public IP.
Step 4: Create a free VM compute instance

Navigate to Compute > Instances > Create Instance
Give it a catchy name - "OracleCoolDevEnviromement"
Change Image source > "name of the custom image you imported"
shape details as below
Copy in your SSH key.
Step 5: Assign a public IP Address to your new dev server

Navigate to "Instance > Details > Attached VNICs > VNIC Details > IP Addresses"
click to edit the existing private IP address
select "reserved public IP" and select the IP address you generated in step 3.
Step 6: Log in / Create an SSH tunnel

I've only tried this on mac and linux as that's what's available to me but the idea here is to log into the remote box and create an SSH tunnel which maps our local port 8888 to that of the remote machine.

Head to the terminal on your local machine with the following command:

ssh -L :8888:localhost:8888 ubuntu@<ip_address> -i <ssh_private_key_name>)

IP address is your public IP address for this box and ssh private key is the name of the private key you have generated earlier.

Step 7: Fire up the notebooks and connect

After the terminal connects to the remote box just type:

jupyter notebook

This will fire up jupyter with a server on localhost:8888 which we have already conveniently mapped to our own localhost:8888.

Step 8: Access

 Click / copy the link in terminal into your browser to log in! it'll be of the format:

http://localhost:8888/?token=biglongtokenoincludingnumbers78708678andletterssfslkfj

This will fire up a jupyter notebook, as though its running on your local machine, however, with all traffic routed securely to the remote server through the SSH tunnel.

Now you're ready to get started!

Step 9: Bonus Round

You may want to do some work outside notebooks for which a remote VS Code sever is a good option. This youtube video gives you a good overview of how to set that up and can be configured pretty easily for you're remote instance.

Bonus points to anyone who follows this though and I'll write up a quick summary of that soon in another article.

Step 10: Share your experience and reqs!

Share your experience! I found this useful, but obviously it would be good to hear what you think and understand what alternative environments you might want to see easily deployable in the future, so just let us know and we'll make more simple ways to use your free tier available!
