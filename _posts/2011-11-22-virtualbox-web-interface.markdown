---
layout: post
status: publish
published: true
title: VirtualBox Web Interface
author:
  display_name: marius
  login: marius
  email: marius@remote-lab.net
  url: http://remote-lab.net/
author_login: marius
author_email: marius@remote-lab.net
author_url: http://remote-lab.net/
wordpress_id: 92
wordpress_url: http://www.remote-lab.net/?p=92
date: '2011-11-22 03:34:53 +0000'
date_gmt: '2011-11-22 00:34:53 +0000'
categories:
- Linux
tags: []
comments: []
---
<p>I am personally using <a href="https://www.virtualbox.org/">VirtualBox</a> as a virtualization solution as it fits my needs and it integrates well with Ubuntu. I recently found that you can set up a web interface and manage Virtualbox through it. In the following article I'll describe how you can set up the web interface for VirtualBox using <a href="http://code.google.com/p/phpvirtualbox/">phpVirtualBox</a> and I'd also like to thank the developers for doing this great job.</p>
<p>Firstly as any web interface a web server is required so you'll need to install apache web server with php support :</p>
<pre>root@localhost#apt-get install apache2 php5 libapache2-mod-php5</pre>
<p>After this you'll have a fully working webserver with php support having its root directory into /var/www/</p>
<p>Now we will need to install VirtualBox. I prefer using the latest version so we'll install VirtualBox 4.1</p>
<pre>root@localhost#apt-get install virtualbox-4.1</pre>
<p>phpVirtualBox requires vboxwebserv service to run so you'll have to run the following command:</p>
<pre><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;">root@localhost#vboxwebsrv -b --host {ip_address} --port 18083 </span><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;"> 
b: background </span><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;"> 
host: the ip address of the interface used by the server to listen </span><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;"> 
port: the port used  by the server</span></pre>
<p>Download phpVirtualBox and move it to you web server's root</p>
<pre><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;">root@localhost#wget http://phpvirtualbox.googlecode.com/files/phpvirtualbox-4.1-5.zip 
</span><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;">root@localhost#unzip  phpvirtualbox-4.1-5.zip 
</span><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;">root@localhost#mv phpvirtualbox-4.1-5 /var/www/vbox 
</span><span class="Apple-style-span" style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px; white-space: pre;">root@localhost#cd /var/www/vbox</span></pre>
<p>Edit the config.php file by modifying the following variables to match your system's:</p>
<pre>root@localhost#vim config.php
 /* Username / Password for system user that runs VirtualBox */
 var $username = ‘vbox’;
 var $password = ‘your-password’;
 var $location = ‘http://{ip_address}:18083/’;</pre>
<p>And now you should have an accessible web interface for your VirtualBox which can be accessed at http://{ip_address}/vbox. The default username and password are admin/admin.</p>
<p>In order to access the VMs consoles through the web interface you have to install the <a href="http://download.virtualbox.org/virtualbox/4.1.6/Oracle_VM_VirtualBox_Extension_Pack-4.1.6-74713.vbox-extpack">VirtualBox Extension Pack</a> and set the display settings to activate the remote display.</p>
<p>&nbsp;</p>
<p><img class="aligncenter size-full wp-image-93" title="phpvbsm" src="http://www.remote-lab.net/wp-content/uploads/2011/11/phpvbsm.png" alt="" width="675" height="500" /></p>
<p>&nbsp;</p>