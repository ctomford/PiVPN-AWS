---


---

<h1 id="pivpn-on-aws">PiVPN on AWS</h1>
<p>This guide will walk you through creating your own <a href="http://www.pivpn.io">PiVPN</a> server on <a href="https://aws.amazon.com/">AWS</a> and connecting to it from Windows. PiVPN was originally created to host your own VPN server on <a href="https://www.raspberrypi.org/">RaspberryPi</a>, but is portable to AWS as well.</p>
<p>Not only will this will help protect yourself from being monitored by your ISP, but also protect you from cybercriminals looking to steal your information.</p>
<h2 id="getting-started">Getting Started</h2>
<p>Amazon Web Services offers one year of free virtual server space, provided you use less than predetermined amounts of bandwidth, time, and space. Even if you go over that limit, the cost of running a server image on Amazon’s Elastic Compute Cloud is probably less than you would pay for a VPN subscription.</p>
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
<p>Log into your Amazon Web Service account and head to the EC2 dashboard.<br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_1.png" alt="EC2"></p>
</li>
<li>
<p>On the top right, you can choose the location where we’ll be setting up your VPN. Then click Launch Instance.<br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_2.png" alt="EC2"></p>
</li>
<li>
<p>Choose whatever Linux AMI is listed as “free tier eligible.” At the time of writing this article, I’m choosing <strong>Ubuntu Server 16.04 LTS</strong>. Go on to the next step.<br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_3.png" alt="EC2"></p>
</li>
<li>
<p>Here choose a t2.micro instance that’s also free tier eligible. Click <strong>Review and Launch</strong>.<br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_4.png" alt="EC2"></p>
</li>
<li>
<p>On the next page, click <strong>Edit Security Groups</strong>.<br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_5.png" alt="EC2"></p>
</li>
<li>
<p>You’ll need to edit the security group to only allow traffic from your computer to access the VPN or proxy. You should have one rule already in place for connecting to your server via SSH, which we’ll use later. We’ll need to add another to allow OpenVPN connections, which use port 1194 by default. For simplicity’s sake, click the  <strong>Add rule</strong>  button. Set the  <strong>Type</strong>  to  <strong>Custom UDP</strong>, the <strong>Port Range</strong>  to <strong>1194</strong>, and the Source to  <strong>Anywhere</strong>.<br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_6.png" alt="EC2"></p>
</li>
<li>
<p>Click <strong>Review and Launch</strong><br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_7.png" alt="EC2"></p>
</li>
<li>
<p>If all went well, your page should look similar (your Source IP will be different) with the rules we just created in the “Security Groups” section. Now click <strong>Launch</strong><br>
<img src="https://github.com/ctomford/PiVPN-AWS/blob/master/screenshots/Screenshot_8.png" alt="EC2"></p>
</li>
</ol>

