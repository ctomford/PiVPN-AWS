---


---

<h1 id="pivpn-on-aws">PiVPN on AWS</h1>
<p>This guide will walk you through creating your own <a href="http://www.pivpn.io">PiVPN</a> server on <a href="https://aws.amazon.com/">AWS</a> and connecting to it from Windows. PiVPN was originally created to host your own VPN server on <a href="https://www.raspberrypi.org/">RaspberryPi</a>, but is portable to AWS as well.</p>
<p>Not only will this will help protect yourself from being monitored by your ISP, but also protect you from cybercriminals looking to steal your information.</p>
<h2 id="getting-started">Getting Started</h2>
<p>Amazon Web Services offers one year of free virtual server space, provided you use less than predetermined amounts of bandwidth, time, and space. AWS free tier includes 750 EC2 hours a month. So if this is the only EC2 instance you create, you should not be billed. Even if you go over that limit, the cost of running a server image on Amazon’s Elastic Compute Cloud is probably less than you would pay for a VPN subscription.</p>
<p>List of things you’ll need:</p>
<ul>
<li>An Amazon Web Services account. This requires a credit card, but you’ll only be charged for what you use, which will likely be nothing if you’re prudent about what you’re doing.</li>
<li><a href="https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html">PuTTy</a>. You’ll also need PuTTy’s sister key generation program, PuttyGen.</li>
<li><a href="https://winscp.net/eng/download.php">WinSCP</a>, or an equivalent FTP client to move files between your local computer and your EC2 instance.</li>
<li>A basic working knowledge of Unix commands and how servers work with clients will be massively helpful in troubleshooting should something not go exactly as planned.</li>
<li><a href="https://openvpn.net/index.php/open-source/downloads.html">OpenVPN GUI</a>, installed in the default location and with the default settings on your PC</li>
</ul>
<h2 id="setting-up-aws">Setting up AWS</h2>
<p>Once you’ve signed up for an Amazon Web Services account, here’s how to launch the server that we’ll use for our VPN:</p>
<ol>
<li>
<p>Log into your Amazon Web Service account and head to the EC2 dashboard. <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_1.png" alt="EC2"></p>
</li>
<li>
<p>On the top right, you can choose the location where we’ll be setting up your VPN. Then click Launch Instance. <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_2.png" alt="EC2"></p>
</li>
<li>
<p>Choose whatever Linux AMI is listed as “free tier eligible.” At the time of writing this article, I’m choosing <strong>Ubuntu Server 16.04 LTS</strong>. Go on to the next step. <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_3.png" alt="EC2"></p>
</li>
<li>
<p>Here choose a t2.micro instance that’s also free tier eligible. Click <strong>Review and Launch</strong>. <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_4.png" alt="EC2"></p>
</li>
<li>
<p>On the next page, click <strong>Edit Security Groups</strong>. <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_5.png" alt="EC2"></p>
</li>
<li>
<p>You’ll need to edit the security group to only allow traffic from your computer to access the VPN or proxy. You should have one rule already in place for connecting to your server via SSH, which we’ll use later. We’ll need to add another to allow OpenVPN connections, which use port 1194 by default. For simplicity’s sake, click the  <strong>Add rule</strong>  button. Set the  <strong>Type</strong>  to  <strong>Custom UDP</strong>, the <strong>Port Range</strong>  to <strong>1194</strong>, and the Source to  <strong>Anywhere</strong>. <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_6.png" alt="EC2"></p>
</li>
<li>
<p>Click <strong>Review and Launch</strong> <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_7.png" alt="EC2"></p>
</li>
<li>
<p>If all went well, your page should look similar (your Source IP will be different) with the rules we just created in the “Security Groups” section. Now click <strong>Launch</strong> <img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_8.png" alt="EC2"></p>
</li>
<li>
<p>Now you’ll want to create a key pair, which sort of works like a password that you’ll use to connect to the virtual server you’re creating. Select “<strong>create a new key pair</strong>” from the dropdown menu and name it whatever you like. Click the button to download the key pair. Store it somewhere safe.<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_9.png" alt="EC2"></p>
</li>
<li>
<p>The next page should alert you that the instance is launching. Scroll to the bottom and hit <strong>View instances.</strong> Here you’ll see a list of any instances you’ve launched, which if this is your first time using EC2 will just be one.<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_10.png" alt="EC2"></p>
</li>
</ol>
<h2 id="connecting-to-the-server-with-putty">Connecting to the server with PuTTy</h2>
<p>We can connect to our EC2 instance with PuTTy, but first we’ll need a proper key file to get connected. When you installed PuTTy, you should have also installed PuTTygen. Go ahead and run it now. PuTTy and PuTTygen both run right out of the box as .exe files with no need to install.</p>
<ol>
<li>
<p>Open PuTTygen, click <strong>Load</strong>.<br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_11.png" alt="EC2"></p>
</li>
<li>
<p>Navigate to the .pem key pair file you downloaded before and load it into Puttygen. You’ll have to select the option to show all file types for the .pem key to show up. Hit <strong>Save Private Key</strong>. The file name must be identical to the .pem key.</p>
</li>
<li>
<p>Now close out of PuTTygen and open PuTTy. Copy your instance’s public IP from the EC2 console into PuTTy. Type in a name for your session and hit save.<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_12.png" alt="EC2"></p>
</li>
<li>
<p>In the left pane, navigate to <strong>Auth</strong> under SSH. Click the browse button at the bottom and navigate to the private key you just generated.<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_13.png" alt="EC2"></p>
</li>
<li>
<p>Back on the main Session page, name and save your session profile so you can quickly connect the next time you use PuTTy. Then click <strong>Open</strong>.</p>
</li>
<li>
<p>A prompt will appear asking you for a username. For the Amazon Ubuntu AMI, it’s <code>ubuntu</code>.</p>
</li>
</ol>
<h2 id="setup-openvpn-on-the-server-and-windows">Setup OpenVPN on the server and Windows</h2>
<p>OpenVPN is a free open source tool that will let you run a full-on VPN through your Amazon EC2 instance. That means all your internet traffic goes through it.</p>
<ol>
<li>
<p>Connect to your EC2 instance using PuTTy according to the instructions above. Once logged in update and install the packages on the server:<br>
<code>sudo apt-get update &amp;&amp; sudo apt-get upgrade -y</code><br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_14.png" alt="EC2"></p>
</li>
<li>
<p>Start the PiVPN installer:<br>
<code>curl -L [https://install.pivpn.io](https://install.pivpn.io/) | bash</code><br>
For more detail on this, see <a href="http://www.pivpn.io/#tech">http://www.pivpn.io/#tech</a></p>
</li>
</ol>
<p>You are then guided through the process of installing all the required software and configuring the VPN server:<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_15.png" alt="EC2"></p>
<p><img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_16.png" alt="EC2"></p>
<p><img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_17.png" alt="EC2"></p>
<p><img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_18.png" alt="EC2"></p>

