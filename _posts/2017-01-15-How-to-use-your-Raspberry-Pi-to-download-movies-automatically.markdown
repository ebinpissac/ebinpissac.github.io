---
layout: post
title:  "How to use your Raspberry pi to download movies automatically"
date:   2017-01-15 22:11:43 +0800
categories: technology
author: "Ebin"
---
<p><strong>DISCLAIMER:&nbsp;</strong></p>
<p><strong>This is for educational purposes only. Do everything at your own risk. Using bit torrent is not illegal, but downloading copyrighted materials are.</strong></p>
<p><strong>Do not use this in a production environment. We are considering zero security in this case. </strong></p>

<p><strong>What we are going to build:<br /></strong></p>
<p>An always on torrent machine that periodically checks for new torrents as defined and download them.</p>
<p>&nbsp;</p>
<p><strong> What we will be using:</strong></p>
<p>&bull;&nbsp;&nbsp; &nbsp;Raspberry pi 3 model b with Raspbian Jessie installed.<br />&bull;&nbsp;&nbsp; &nbsp;Official raspberry pi 3 PSU as third party PSU's may not provide enough power<br />&bull;&nbsp;&nbsp; &nbsp;External HDD<br />&bull;&nbsp;&nbsp; &nbsp;USB fan or heatsink (Recommended, not necessary)<strong><br /></strong></p>
<p><strong>&nbsp;</strong></p>
<p><img title="" src="https://qebarq-bn1305.files.1drv.com/y3m5TwxP_aHuBXCTbVtLDvRIYJjI0bt-K0k0drRz22u46AVz8jc7hBKooNSrxsCRGKGgbM2vBPI31pz5rmb-OFxSggkDhhABq8ccZXnWPwwDVPTSHlBNUmq9JW2Req3dtDPlK1-qo21jz5Up476RXfMIaQejv4Umpic-5WSqIsePEI?width=1116&amp;height=800&amp;cropmode=none" alt="" width="800" height="600" /></p>
<p><strong>Assumptions:</strong></p>
<p>&bull;&nbsp;&nbsp; &nbsp;You have vnc / physical access with GUI to your pi<br />&bull;&nbsp;&nbsp; &nbsp;You have a user named pi with sudo access (this available by default)<br />&bull;&nbsp;&nbsp; &nbsp;You know the password of user pi<strong><br /></strong></p>
<p><strong>Note: We will be doing most of the configurations in a terminal.</strong></p>
<p><strong><br />1) Install and enable apache webserver<br /></strong>This will help to access the contents over a webserver. Install it by typing<strong><br /></strong></p>
<div class="example">
<div class="highlight">
<pre>sudo apt-get install apache2
</pre>
</div>
</div>
<p>Once installed, start and enable it</p>
<div class="example">
<div class="highlight">
<pre>sudo systemctl enable apache2
sudo systemctl start apache2
</pre>
</div>
</div>
<p>Give everyone full permission to the documentroot</p>
<div class="example">
<div class="highlight">
<pre>sudo chmod 777 /var/www/html
</pre>
</div>
</div>
<p><strong>2) Connect the hard disk, create mountpoint and setup automount</strong></p>
<p>&bull;&nbsp;&nbsp; &nbsp;Connect your hard disk to the pi. Make sure that it works by accessing the contents. If you see a lightning bolt symbol on the top right corner of the screen, that means the pi is not getting enough power to work properly. Use the official PSU in this case.<br />&bull;&nbsp;&nbsp; &nbsp;Open a terminal, and see where the hard disk is mounted, by using the following command<strong><br /></strong></p>
<div class="example">
<div class="highlight">
<pre>df
</pre>
</div>
</div>
<p><img src="https://earrcg-bn1305.files.1drv.com/y3m2x1N35pnRTTiHC2I8lH5evw3YFErqCcaNCkPQEaqz5Y6uzSD3j2ff4RfpSYyWDXOnNuvHaCwUgQTKWGnNL8NE_P0KbtzYbI0O9fB_Nbjf9uyaMWO-8LRI2M-8AIoMmKuXSsSNnFOFpWsmdFk5-SO9SPghYOJGCW5zDJoqsDAqRo?width=803&amp;height=293&amp;cropmode=none" alt="" width="800" height="400" /> 
In this case, it is mounted in<em> /var/www/html</em>. This will not be your case. It will probably in something like <em>/media/pi/xxx</em> . Whatever it is take note of it. Let us call this value as <em>initial_mount_point</em></p>
<p><em>&bull;&nbsp;&nbsp; </em>&nbsp;Get the block id of the hard disk. Take note of the value starting from UUID</p>
<p><img src="https://diqzcq-bn1305.files.1drv.com/y3mIoD4ZAkd1ENHivYdmvX_ryryQniJMAxYc3LnDlTSNxx24_1a3mlYCjgNkwqprpceU2c_nv4E3T3zTjNKGhKFjV3P0kBOGF1KO6xwShFF5C8BS76HQY2gS2U-QVeIIMCgMbLaiTPTRKFg-wUUfXoRKkGqb9QTHfIlyz4i0rfYOnU?width=1122&amp;height=176&amp;cropmode=none" alt="" width="800" height="300" /></p>
<p>&bull;&nbsp;&nbsp; &nbsp;Configure fstab so that the hard disk will mount to a certain point after reboot</p>
<div class="example">
<div class="highlight">
<pre>sudo nano /etc/fstab
</pre>
</div>
</div>
<p>&bull; This will open the fstab file in nano text editor in the terminal. Navigate to the end of the line, and type the following</p>
<p><em>UUID=&rdquo;xxxxxxxx&rdquo; /var/www/html ntfs defaults 0 0</em></p>
<p>Where &ldquo;xxxxx&rdquo; is the block id that you got from the previous step.</p>
<p>&nbsp;</p>
<p><img src="https://eatsfg-bn1305.files.1drv.com/y3mwdrpm-UQqhfb4UZRNdU9Bhv1qLzTaLwX3nCtdKleUsVrUlySJlMyzLX-XmcV3ecbZxBWddIpwpkpyt_-aTi2Td0ArypfciVd6q2g_E0uXaYIq6tfOXlC-Mw9aw0CZuUyE4MQ_kPqa1RNq9NyUi6-hgp8Ua7hapCNOHV94aykU6c?width=804&amp;height=236&amp;cropmode=none" alt="" width="800" height="400" /></p>
<p>Type CTRL+O to save and CTRL+X to exit the editor.</p>
<p>&bull;&nbsp;&nbsp; &nbsp;Unmount the hard disk from initial mount point</p>
<div class="example">
<div class="highlight">
<pre>sudo umount initial_mount_point
</pre>
</div>
</div>
where initial_mount_point is what you got from the first step, which is something like /media/pi/xxxx
•	Now mount the hard disk to the new mount point.
<div class="example">
<div class="highlight">
<pre>sudo mount –a
</pre>
</div>
</div>
•	Verify that it worked by typing the df command. You should get output like this.

<img src="https://earrcg-bn1305.files.1drv.com/y3m2x1N35pnRTTiHC2I8lH5evw3YFErqCcaNCkPQEaqz5Y6uzSD3j2ff4RfpSYyWDXOnNuvHaCwUgQTKWGnNL8NE_P0KbtzYbI0O9fB_Nbjf9uyaMWO-8LRI2M-8AIoMmKuXSsSNnFOFpWsmdFk5-SO9SPghYOJGCW5zDJoqsDAqRo?width=803&height=293&cropmode=none" width="800" height="400" />
<p><strong>3) Install and configure flexget</strong></p>
<p>Felxget will be the tool that we use to download torrent files based on our preferences. The following information is from their website.<strong><a href="(https://www.flexget.com/InstallWizard/Linux)">(https://www.flexget.com/InstallWizard/Linux)</a><br />&bull;&nbsp;&nbsp; &nbsp;Python<br /></strong>FlexGet requires Python 2.7, 3.3 or newer to run. You can check your version with command.<strong><br /></strong></p>
<div class="example">
<div class="highlight">
<pre>python -V
</pre>
</div>
If it is not available, install it by using the following command in terminal</div>
<div class="example">
<div class="highlight">
<pre>sudo apt-get install python3.5
</pre>
</div>
&bull; <strong>PIP</strong></div>
<div class="example">Second piece of required software is python package manager called PIP. This can be usually found from operating system package repository under name python-pip or python3-pip. If you install python3-pip it may need to be used via command pip-3.5 or something similar.<br />Install it by using the following command</div>
<div class="example">
<div class="highlight">
<pre>sudo apt-get install python-pip5
</pre>
</div>
<strong>&bull; Upgrade setuptools</strong></div>
<div class="example">Using latest setuptools will save headaches in some older installations, this can be achieved with</div>
<div class="example">
<div class="highlight">
<pre>sudo pip install --upgrade setuptools
</pre>
</div>
</div>
<p><strong>&bull; Install in a virtualenv</strong></p>
<p>This is the recommended way unless you want multiple accounts in the system to be able to use FlexGet without each having to install it themselves.</p>
<p>Install virtualenv:</p>
<div class="example">
<div class="highlight">
<pre>sudo pip install virtualenv
</pre>
</div>
</div>
<p>Create Virtualenv: This creates isolated python environment. You can create as many of these as you like for each python application you use.</p>
<div class="example">
<div class="highlight">
<pre>virtualenv ~/flexget/
</pre>
</div>
</div>
<p>Install FlexGet in the virtualenv:</p>
<div class="example">
<div class="highlight">
<pre>cd ~/flexget/
bin/pip install flexget
</pre>
</div>
</div>
<p>Create a folder to save the torrent files that are downloaded.</p>
<div class="example">
<div class="highlight">
<pre>sudo mkdir /home/torrents
sudo chmod 777 /home/torrents
</pre>
</div>
</div>
<p>&bull;<strong> Configure flexget to download torrents automatically</strong></p>
<p>This is the most important and confusing part. Here I am sharing only what I did and found to be working. You can find many other configurations from <a href="https://www.youtube.com/watch?v=zZupthYLZ5Q&amp;list=PLqv94xWU9zZ0pVGrgKtMuhFHun8-MahSY&amp;index=2">here </a>and <a href="https://www.flexget.com/Configuration">here.</a></p>
<p>We will configure only to download new Malayalam movies published in extratorrent.cc , English movies published in yts.ag and new episodes of Sherlock from anywhere. <br /><br />To do that,<br />Navigate to the install directory</p>
<div class="example">
<div class="highlight">
<pre>cd ~/flexget
</pre>
</div>
</div>
<p>Create a configuration file using nano editor</p>
<div class="example">
<div class="highlight">
<pre>nano config.yml
</pre>
</div>
</div>
<p>Type the following:</p>
<p><em>tasks:</em><br /><em>&nbsp; Download Sherlock:</em><br /><em>&nbsp;&nbsp;&nbsp; rss: https://showrss.info/show/269.rss</em><br /><em>&nbsp;&nbsp;&nbsp; series:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Sherlock</em><br /><em>&nbsp;&nbsp;&nbsp; regexp:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; accept:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - 720p</em><br /><em>&nbsp;&nbsp;&nbsp; download: /home/torrents/</em><br /><em>&nbsp; Download movies:</em><br /><em>&nbsp;&nbsp;&nbsp; rss: http://extratorrent.cc/rss.xml?type=search&amp;search=malayalam+2016</em><br /><em>&nbsp;&nbsp;&nbsp; regexp:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; accept:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - DVDRip</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; reject:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - cam</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - 2GB</em><br /><em>&nbsp;&nbsp;&nbsp; download: /home/torrents/</em><br /><em>&nbsp; Download english movies:</em><br /><em>&nbsp;&nbsp;&nbsp; rss: https://yts.ag/rss/2016/720p/all/5</em><br /><em>&nbsp;&nbsp;&nbsp; regexp:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; accept:</em><br /><em>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - yts</em><br /><em>&nbsp;&nbsp;&nbsp; download: /home/torrents/</em></p>
<p>&nbsp;</p>
<p><img src="https://di83tg-bn1305.files.1drv.com/y3mSejjSsK5h8htVEWDIhIvpRavHlMhMna1hH9FehqBSE65lDHXgi-Mt-vLM97_Z1b-GbguhgDlee3zM8v-Hg3lwA4oCT7nu9a1W03eAXFnH2iuije1M6jSVORhK3F6uTd3MSf-oiinhSMAe_4exmoKik-189caT9lBfr1vAEMKCsc?width=1132&amp;height=660&amp;cropmode=none" alt="" width="800" height="400" /> <p><strong>IMPORTANT: DO NOT USE TABS, INSTEAD USE SPACES. WATCH THE ABOVE VIDEO AND READ THROUGH THE SITE FOR MORE INFO ON HOW TO FILL THIS FILE</strong></p></p>
<p>CTRL+O and CTRL+X to save and exit</p>
<p><strong>&bull;&nbsp;&nbsp; &nbsp;Test the configuration</strong><br />Navigate to the flexget install folder and make it executable</p>
<div class="example">
<div class="highlight">
<pre>cd ~/flexget/bin
sudo chmod +x flexget
</pre>
</div>
Test the configuration by following command:</div>
<div class="example">
<div class="highlight">
<pre>./flexget --test execute
</pre>
</div>
</div>
<p>If your output is similar to the following, there is something wrong with your config. <img src="https://dipc2q-bn1305.files.1drv.com/y3mVb72Ql4M3J7nzSygh-sI5R5YGF9vkLRcBGtS5PHKl5GjgS1VUctYG3nBBzfrZBlr1eA7PrCCOUVITJsz_eJB7MEl7Y1vA3iFE7QS4u807gDCn20a_CAzZKcr0cJvg3HkdfkvmUgHJa-P-LtSAEe-d_AEKLUlEnmwLE9Ox-ZRyHM?width=1734&amp;height=578&amp;cropmode=none" alt="" width="800" height="400" /> 
<p>&nbsp;</p><p>If everything is OK, you will get something like this:</p> <img src="https://eaqbiq-bn1305.files.1drv.com/y3mllHI0zR7csl1CjCsDk--kmmiexEnbw6OlkNeC5M3v0hfYbHy5yoDzWf6OosnHzKjv2wW4l70xQ0CIAA-bZoOOh9Bi-C_LET1Y0pF8iVPBoiYCTlsJ2CZruGXBByJn61m6vX5Y77MDEs1Qe4GkyIlOUUdxE09jAhoAkLJXYUOQOc?width=1856&amp;height=409&amp;cropmode=none" alt="" width="800" height="400" />
<p>Now let us execute flexget to download the torrent files.</p></p>
<div class="example">
<div class="highlight">
<pre>./flexget execute
</pre>
</div>
</div>
<p>This will download the torrent files to the folder we created earlier, to /home/torrents. Let us verify</p>
<div class="example">
<div class="highlight">
<pre>ls /home/torrents
</pre>
</div>
</div>
<p><img src="https://qeb2lg-bn1305.files.1drv.com/y3mw93vkAYR0aMSqIG_n7sac3wWiDqYh--BdMlbUTu0Q1ziC0Ct-iCNPjqyXjK1FHek7AXXUTB7q5KQn_v6Nu1O8z14rac-fiTSmrNLywwMZ5SAM90GfwL4S4zLrpSv3QZGDd3CchRGzg9-jvBfZHa6DInHOe53B56NzWdhBsDu-fs?width=1366&amp;height=217&amp;cropmode=none" alt="" width="800" height="300" /><p> There will be a lot of .torrent files. Mine has that .added name because all of them are already completed downloading.</p></p>

<p><strong>4) Install and configure transmission to download the files using the torrent files already downloaded.</strong></p>
<div class="example">
<div class="highlight">
<pre>sudo apt-get install transmission-gtk
</pre>
</div>
</div>
<p><p>Open transmission from the start menu </p><img src="https://qezu2w-bn1305.files.1drv.com/y3mJ3ebtrImXZFJXC1t0PjzSnchdayCUOFk84UvmIfpRX3Yf7kWp8bNX20hTiQGsZ7vLmUHZBHoGiXp-J9NmNRLaJjxWg-mtSYJN2TWBUi43eKSkvXtFIj2kTYnd6rNO4H7SXMcZ2KEBwjkTfBv6OhV8vWtzl-WhH3HRQR4rCWc-kw?width=542&amp;height=572&amp;cropmode=none" alt="" width="800" height="600" /> <p>Go to edit-preferences, Set the speed limits you want from Speed tab </p><img src="https://qezbig-bn1305.files.1drv.com/y3mRL_AGbQZimEDOqQ60jZVStSn6FPnVWsnOQFTQTJB4tS3vfjU7GkyHaDJ6Ggdk6qyRxpEasTBrYbi6-4niYfZLooUgc4iJgsB8ueuLHRT_ccoX4XkzkRbRne8-mw1xpbilKQBpiCfOE204oiJEpua92hce9NvH5Rq4fMcUinA5RY?width=812&amp;height=582&amp;cropmode=none" alt="" width="800" height="600" /> <p>In the downloading tab,</p> <img src="https://qea5zg-bn1305.files.1drv.com/y3mDSDjv7J-tPrb0TduihwuEiv6QcWrEwAfILGl7hSjfuMcQmXlJ9svw7rTlYi4Zhe9Z41xzrvK8yTEirrmk4COFoizQ7bDevsg-mY-PwPx58dvggkQkrk6ehIWLfpjwElKU_qIe_o718a7764IN5l7pZSQHD3bHOO57jTrUnnxLo4?width=840&amp;height=575&amp;cropmode=none" alt="" width="800" height="600" /> <p>In automatically add .torrent files from, select /home/torrents folder Untick show the Torrent Options Dialog Tick Start added torrents In Save to Location, navigate to your hard disk mount point, which is /var/www/html and select the folder you want.</p> <img src="https://qeakxa-bn1305.files.1drv.com/y3mcZArcURhu4wJtxWdXE9DVtzAAX5oXHPMsejsafo7FcwrbPLEiK6BDH5D30LCK3CQ6cfushfWjj5-1NW6ehvtVCMSFglUJOqE5T8curT8e4imrrkyRK-LEwVZLbntGlGiAo_ufzqL9CxH-2x8S2ye_SOY5hWBiXwhzG89aHeMsiI?width=1094&amp;height=585&amp;cropmode=none" alt="" width="800" height="600" /> <p>You can leave the other settings as it is, or change it if you need. Now wait a few minutes and you can see that the torrents start downloading.</p></p>
<p><strong>5) Configure flexget to check for torrents periodically</strong></p>
<p>We will setup flexget to check for new torrents every hour and download it if found. For that, we will use crontab, which is pre installed in the system.</p>
<p>&nbsp;</p>
<p>Test the configuration by following command:</p>
<div class="example">
<div class="highlight">
<pre>crontab &ndash;e
</pre>
</div>
</div>
<p>This will open the crontab file in a text editor. Navigate to the end and add the following lines:</p>
<p><em># to run flexget without generating log files</em><br /><em>20 * * * *&nbsp; /home/pi/flexget/bin/./flexget execute</em><br /><br /><em># start transmission-gtk version minimized if it is not already running</em><br /><em>*/30 * * * * export DISPLAY=:0 &amp;&amp; if pidof -x /usr/bin/transmission-gtk; then exit; else /usr/bin/transmission-gtk -m; fi &amp;</em></p>
<p>CTRL+O and CTRL+X to save and exit</p>
<p>By now, you have your pi configured to check for new torrents every hour and download it to the hard disk connected to it. In the following steps, we will make it accessible via a web browser or as a network drive.</p>
<p><strong>6) Configure a static ip address to the pi</strong></p>
<div class="example">
<div class="highlight">
<pre>sudo nano /etc/dhcpcd.conf
</pre>
</div>
</div>
<p>Go to the end of the file and add the following:</p>
<p><em>interface eth0</em><br /><br /><em>static ip_address=192.168.1.200/24</em><br /><em>static routers=192.168.1.1</em><br /><em>static domain_name_servers=192.168.1.1</em><br /><br /><em>interface wlan0</em><br /><br /><em>static ip_address=192.168.1.200/24</em><br /><em>static routers=192.168.1.1</em><br /><em>static domain_name_servers=192.168.1.1</em></p>
<p>Here, you need edit based on your needs. My router address is 192.168.1.1 and I set my ip to be 192.168.1.200</p>
<p><img src="https://earkww-bn1305.files.1drv.com/y3mXRY523NmMovpAvDbRpbirAcdGkEd2pAq1fgf7Chy3YMZCCPoG4GH3gOFGtwVyjYNUcCAHrXBRU1DSowlB2J7iR4ChrkBWY8bB-Sg7ngUfyDuCjGFdUjQoaVrTJoezoLVxltBG6qRMs2iOtt1zahXxfo3BpGy_b-_f40de6dQUFQ?width=909&amp;height=463&amp;cropmode=none" alt="" width="800" height="600" /></p>
<p><strong>7) Configure apache web server.</strong></p>
<p>We will use the default configuration settings here. To verify it,</p>
<div class="example">
<div class="highlight">
<pre>sudo  nano /etc/apache2/sites-enabled/000-default.conf
</pre>
</div>
</div>
<p><p>Just make sure that the DocumentRoot is set to be /var/www/html. If it is not, edit it. Mostly there is no need to edit.</p> <img src="https://dipjia-bn1305.files.1drv.com/y3mfz1gx4SNN4-xRdl7mLHpBRvsv6ntXWtT0m_J-uK74Q3tsTAJI4NPew6IMio1FWowQH4SL0QOgnK59Lq3ROs-e48xwz12mgbw1VU-3g6U3dyUHJuiYqwU2LGsheZATapyBm2gXC1LsRuuUmc5bYqunq0h__P_J_DgaqtGMtK7I0g?width=1092&amp;height=493&amp;cropmode=none" alt="" width="800" height="400" /> <p>Restart the web server</p></p>
<div class="example">
<div class="highlight">
<pre>sudo systemctl restart apache2
</pre>
</div>
</div>
<p>Verify that it works by going to the webpage from a browser. Note that this device should be connected to your home network. You should be able to access the hard disk contents now. <img src="https://di8plq-bn1305.files.1drv.com/y3mqOgXmpJRFFALlar2T-gtQ83eUeT5OdB5gb5xES6itK00DRh_xse31nDAN1dVnTVA5KAd5THJwiDwnl2QDnopJT6liQVbBKfuQmBGoQq6PuS2v5FtEnWXyDMGdouPVhUNZHmgLCSZ1en2cb0IvS0jl0CcsLW0Qr0QQ_X4rWGuyPg?width=967&amp;height=648&amp;cropmode=none" alt="" width="800" height="600" /> <p>You will be able to watch videos from your browser. </p><img src="https://qebvtw-bn1305.files.1drv.com/y3mzUF2zZAlUPWkmEjt-osQVr4xQXR0A0G54y0TSIiZpLkRHWwYN74gqDXBc2bwF4P55DnEhokkh3F7XDJddwSmgmXuqPsGFMgiCV1kdxKiv3ypp41lSd5VY0axx3IcixRzrwlG7QM7tG0uccZUt_3Hruk2D84I4FkWS-uwnbiV83c?width=1771&amp;height=920&amp;cropmode=none" alt="" width="800" height="600" /></p>
<p><strong>8) Install and configure samba for using as a network drive</strong></p>
<p>&nbsp;</p>
<div class="example">
<div class="highlight">
<pre>sudo apt-get install samba
sudo nano /etc/samba/smb.conf
</pre>
</div>
</div>
<p>This will open samba configuration file in nano. Go to the end of the file and type the following, and save and exit.</p>
<p><em>[movies]</em><br /><em>&nbsp; path = /var/www/html/</em><br /><em>&nbsp; public = yes</em><br /><em>&nbsp; read only = yes</em><br /><em>&nbsp; guest ok = yes</em><br /><em>&nbsp; browseable = yes</em></p>
<p><img src="https://eate8a-bn1305.files.1drv.com/y3mWP1fHhF_DMuSkRDiGadvNBQhJLLhM6LIrQx09agCwNT8-ukSTCKVEYJ1iOITO6E1TSHao8PZUDCTuRxAi5I72bWW3R6JEaQgz2I1BSsFAE7I_YkK3ikQ-L3F5riWnpXO2WI1WCUvZeYXRvYE0bquyInqdi-fu3tLL6xWEDESZiU?width=895&amp;height=578&amp;cropmode=none" alt="" width="800" height="40" /> <p>Enable and restart samba.</p></p>
<div class="example">
<div class="highlight">
<pre>sudo systemctl enable smbd
sudo systemctl restart smbd
</pre>
</div>
</div>
<p>Now you can map this as a network drive in your windows PC. To do this in windows 10, right click My PC, then click Map network drive.</p>
<img src="https://easara-bn1305.files.1drv.com/y3mHPhBCd6LVOyvhvQfwet_40pfGXVzIYjA5oBgIUsjK2XPRMQqSoi7xjD3ryV1sp7cf3CCAPY0ZyQpAQ5kg8PSrhcR4IhpFsNujajhs554duPBVKgEb4gzTEzNmJsk-X7-KBexwT6iWO6dAjx0wfrkTK4yuCGmErNK-uRcgQO_M4c?width=459&height=605&cropmode=none" width="800" height="400" />
<p>Enter the ip address and share name as follows. Share name is movies as we configured earlier. Check connect using different credentials. Enter username as pi and the password to connect.</p>
<img src="https://easvtg-bn1305.files.1drv.com/y3m-A783RrchKFNg5yPOhlerPt2o2nOxa0WnfHIchM45eJkmuNLksNldMgxFw5RxW_5NzlULPpEJKc9CnerreDgLhUOAqHowi_Rrl5Qu60Y-TwQ8Jas3-0XVaAq_fexOiREyhg0WtDpLL5pHZ_9SAR4nvd8FCfwqEyHzExQEbvH9KE?width=1379&height=703&cropmode=none" width="800" height="400" />
<p>You should have it connected as a drive now.</p>
<img src="https://eatlnw-bn1305.files.1drv.com/y3mP4d2arH_5xB-KZAv-ScyfXMtPA441WLRGyLdsJpa0izd3wE2GvEC_f7X5EUoGILefDhskhcfpSvAd21imPQHdExBQ40SQpvUb6z5XpJlidQtJvQE2RKX360kkq8-cXZ4wPEgWgpIT5WkGnZNxDV9_rjhE9_BB96FUpozGkux9I0?width=1365&height=484&cropmode=none" width="800" height="400" />

