---
layout: post
title: HSRP Timers tuning and Authentication
date: '2012-02-22 20:50:59 +0000'
categories:
- Switching
- Routing
permalink: hsrp-timers-tuning-and-authentication
---
In today's post we are going to continue with some HSRP implementations. We have the following topology consisting of 2 x L3 switches, 1 x L2 switch and 2 hosts located in 2 separate VLANs. On the L2 switch ports Fa0/3 and Fa0/4 are set as access ports and ports Fa0/1 and Fa0/2 are set as trunks. Also Fa0/1 on the 2 x L3 switches are set as trunks. On the L3 switches we'll also need to set 2 SVIs used for routing between the 2 vlans we have in place.


___

<img class="aligncenter size-large wp-image-125" title="HSRP2" src="{{'/public/images/HSRP2-1024x822.png' | prepend: site.baseurl | prepend: site.url }}" alt="" width="550" height="441" />

Going forward to the actual HSRP configuration, we'll set HSRP group 0 for both the SVIs configured on the L3 switches. By default the active router in the HSRP group sends hello messages once every 3 seconds. The standby router is listening and if the hellos are missed for the duration of the holdtime timer ( 10 seconds by default ) the standby router is free to asume its active role. So this implies a 10 seconds downtime if one of the routers goes down which is unacceptable in todays networks. In order to avoid this kind of situation, we'll set the hello messages to be sent once every 200ms and the holdtimer to be 600ms so that the actual downtime will be less than a second. This will increase the bandwidth usage for the control plane messages but I don't think this should be a problem for nowadays links.

{% highlight bash %}
interface Vlan10
 ip address 192.168.10.2 255.255.255.0
 standby 0 ip 192.168.10.1
 standby 0 timers msec 200 msec 600
end
interface Vlan20
 ip address 192.168.20.2 255.255.255.0
 standby 0 ip 192.168.20.1
 standby 0 timers msec 200 msec 600
end
{% endhighlight %} 

In order to prevent unathorized routers to become part of the HSRP group we need to set an authentication method for the participating devices. You can implement either a plain text or a MD5 authentication method for the HSRP group. With the plain text authentication HSRP messages are sent containing a plain text key string. If the key string in a message matches they key configured on the HSRP router then the message is accepted. With the MD5 authentication a message digest 5 hash value is sent along with the HSRP messages. This value is calculated based on a portion of the message using a secret key string. When the message is received the router recalculates the hash function of the received message and if it matches the hash value received within that message then it's accepted. MD5 authentication is recommended as it's far more secure than the plain text authentication. While when using the plain text authentication messages can be sniffed and the actual key string can be seen in the message, with MD5 it's extremely difficult to reverse the hash value.

{% highlight bash %}
###############PLAIN TEXT AUTHENTICATION##################
interface Vlan10
 ip address 192.168.10.2 255.255.255.0
 standby 0 ip 192.168.10.1
 standby 0 timers msec 200 msec 600
 standby 0 authentication text AUTH_KEY
!
interface Vlan20
 ip address 192.168.20.2 255.255.255.0
 standby 0 ip 192.168.20.1
 standby 0 timers msec 200 msec 600
 standby 0 authentication text AUTH_KEY
end
########## MD5 Authentication########################
interface Vlan10
 ip address 192.168.10.2 255.255.255.0
 standby 0 ip 192.168.10.1
 standby 0 timers msec 200 msec 600
 standby 0 authentication md5 key-string AUTH_KEY
end
interface Vlan20
 ip address 192.168.20.2 255.255.255.0
 standby 0 ip 192.168.20.1
 standby 0 timers msec 200 msec 600
 standby 0 authentication md5 key-string AUTH_KEY
end
{% endhighlight %} 

You may see in the following captured packets that the plain text key actually appears in the HSRP header, while when using MD5 auth part of the payload is hased using the key string

Plain text auth
<a href="{{'/public/images/plain_text_auth.png' | prepend: site.baseurl | prepend: site.url }}"><img class="size-large wp-image-126 aligncenter" title="plain_text_auth" src="{{'/public/images/plain_text_auth.png' | prepend: site.baseurl | prepend: site.url }}" alt="" width="550" height="275" /></a>

MD5 Auth
<a href="{{'/public/images/md5auth.png' | prepend: site.baseurl | prepend: site.url }}"><img class="size-large wp-image-127 aligncenter" title="md5auth" src="{{'/public/images/md5auth.png' | prepend: site.baseurl | prepend: site.url }}" alt="" width="550" height="270" /></a>
