---
layout: post
title:  "How to use your Raspberry pi to download movies automatically"
date:   2017-01-15 22:11:43 +0800
categories: technology
author: "Ebin"
---
What we will be using:
Raspberry pi 3 model b with Raspbian Jessie installed.
Official raspberry pi 3 PSU as third party PSU's may not provide enough power
External HDD
USB fan or heatsink (Recommended, not necessary)
<img src="https://qebarq-bn1305.files.1drv.com/y3m5TwxP_aHuBXCTbVtLDvRIYJjI0bt-K0k0drRz22u46AVz8jc7hBKooNSrxsCRGKGgbM2vBPI31pz5rmb-OFxSggkDhhABq8ccZXnWPwwDVPTSHlBNUmq9JW2Req3dtDPlK1-qo21jz5Up476RXfMIaQejv4Umpic-5WSqIsePEI?width=1116&height=800&cropmode=none" width="1116" height="800" />

Assumptions:
You have vnc / physical access with GUI to your pi
You have a user named pi with sudo access (this available by default)
You know the password of user pi
Note: We will be doing most of the configurations in a terminal.

1) Install and enable apache webserver
This will help to access the contents over a webserver. Install it by typing
</div>
  <div class="example">
        <div class="highlight">
        <pre>
      sudo apt-get install apache2
</pre>
</div>
