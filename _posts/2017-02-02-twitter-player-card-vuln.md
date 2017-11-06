---
layout:     post
title:      "Twitter Player Cards Vulnerability"
subtitle:   "Watching a video on Twitter maybe put your PC or phone in a risk"
date:      2017-02-02 21:41:53
author:     "Mohammed Ali"
category: Web-Security
---

## Summary

Twitter allows the embedding of videos in a tweet via [Player Cards](https://dev.twitter.com/docs/cards/types/player-card). The problem is in the playing method of the video: currently, it uses a sandbox (`iframe`)  to play the embedded video(s) even when this might be protected against [Cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting), still being unsafe because it doesn't protect **HTTP Request Sender**  information such as the **IP Address, User-Agent, and the referer**.
> A lot of Twitter users used to use a VPN to keep these safe from Twitter himself, will they agreed to share them with 3rd-part and this 3rd-part could be anyone

The attacker can embed a link to a request logger or can listen to connections in order to get the IP address of the video viewer.

## Replication Steps

#### Step 1: Adding the pertinent Twitter meta tags to your page
You need to add some Twitter specific meta tags to your page to enable Twitter Player Cards.

|Name|Content|
|--- |--- |
|twitter:card|Type of Twitter card; Must be set to a value of “player”|
|twitter:title|The title of your content as it should appear in the card|
|twitter:site|The Twitter @username the card should be attributed to|
|twitter:description|Description of the content (optional)|
|twitter:player|Listener or Request Logger URL **Must be HTTPS URL**|
|twitter:player:width|Width of IFRAME specified in twitter:player in pixels|
|twitter:player:height|Height of IFRAME specified in twitter:player in pixels|
|twitter:image|Image to be displayed in place of the player on platforms that don’t support iframes or inline players; you should make this image the same dimensions as your player|

Here is a example with a listener in 192.168.1.1:2020:

```html
<meta name="twitter:card" content="player" />
<meta name="twitter:title" content="Twitter cards vulnerability" />
<meta name="twitter:description" content="Twitter cards vulnerability allows to get the video viewer IP, Listen to 192.168.1.1:2020 on your machine then play the video you will be surprised" />
<meta name="twitter:player" content="https://192.168.1.1:2020" />
<meta name="twitter:player:width" content="360" />
<meta name="twitter:player:height" content="203" />
<meta name="twitter:image" content="https://upload.wikimedia.org/wikipedia/de/thumb/9/9f/Twitter_bird_logo_2012.svg/154px-Twitter_bird_logo_2012.svg.png" />
```


----------


#### Step 2:  Launch your page


----------


#### Step 3: Validate your card using the Twitter Validator Tool:
Once you have added the meta tags and published the page, copy and paste the URL into the [Twitter Card Validator](https://cards-dev.twitter.com/validator) and then click **Preview Card**.

![F156985](https://cdn.rawgit.com/xc0d3rz/Abstract-jekyll/c690ee51/img/Screenshot_at_2017-02-02_23_41_50.png)

----------


#### Step 4: Request approval for whitelisting
If your domain has not been whitelisted by Twitter, the Player Cards will not appear. When validating the URL, a message will appear stating that the URL has not been whitelisted.


Click **Request Approval** to begin the approval process. You will be required to provide additional information about your domain. For more information on the approval process, see the [Twitter documentation](https://dev.twitter.com/cards/types/player/approval).

![F156986](https://cdn.rawgit.com/xc0d3rz/Abstract-jekyll/c690ee51/img/Screenshot_at_2017-02-02_23_49_24.png)


----------


#### Step 5: Share it on twitter
When the embedded video is played you might received the viewer(s) IP (I suggest to use a request logger) .

![F156990](https://cdn.rawgit.com/xc0d3rz/Abstract-jekyll/c690ee51/img/Screenshot_at_2017-02-02_23_52_28.png)


## Proof of Concept

I made a video as a proof of concept, testing the vulnerability on a Virtual Machine running on WINXP SP2 (**Computer B**).
The source code can be found at [poc.html](https://github.com/xc0d3rz/twitter-card-backdoor/blob/master/poc.html)

I've hosted the page on a server, shared it on twitter and then played the embedded video in the the Virtual Machine. Eventually I got **B's ip address.** by listening to in the port **2020**  coming from **Computer A** to then attack **Computer B** with metasploit's `ms08_067_netapi` exploit gaining access to **Computer A**.

<iframe width="560" height="315" src="https://www.youtube.com/embed/0occxD9v11M" frameborder="0" allowfullscreen></iframe>



----------


## Timeline:

2017-02-03 19:22:15 +0000: @xc0d3rz (comment)
#### Impact
When attackers know their IP
- They could ISPDox you (Call your ISP up and claim to be with support, claim their lookup system is down and ask for your information). After that they will have your Name and Address, and they could SWAT you or ruin your credit score / dox you by publishing your SSN and other information online.

- They Could attack you by "DDos Attacks"  and harm your computer,phone or tablet

- They Could scan your ports and find open ports  then use "IP Hacking Ways"  Like Metasploit,  Arimtage or any way else, So stealing users IP is very dangerous
all of us know that Twitter is full of Politicians, Famous people or normal users, Attackers can track them or hacking their PC(s).


---

2017-02-07 19:48:26 +0000: @bugtriage-jason (bug needs more info)
Thank you for your report.

Based on your description and video, it appears that this could only reveal an IP address in the same way that an image or hyperlink would. Can you elaborate on how this is a security issue? In your report, you mention that some users use a VPN to avoid revealing their IP address to Twitter. In this case, a VPN would still prevent the user's IP address from being revealed via the method described in your report. Do you believe we are missing anything? If so, please let us know.

Thank you for thinking of Twitter security.

---

2017-02-18 10:56:45 +0000: @xc0d3rz (bug new)
Sorry for the delay I was sick.

>  It appears that this could only reveal an IP address in the same way that an image or hyperlink would. Can you elaborate on how this is a security issue?

Knowing the IP address of user itself a risk, The attacker can attack or target the IP by using a tool for developing and executing exploit code against a remote target machine such as [Metasploit](https://www.metasploit.com/) or Types of remote attacks such as:

#### DoS attacks

DoS, or Denial of Service, is an attempt to make a computer or network unavailable for its intended users. DoS attacks obstruct communications between affected users, preventing them from continuing in a functional way. One common method of attack involves saturating the target machine with external communications requests, so that the target machine cannot respond to legitimate traffic, or responds so slowly as to be rendered effectively unavailable. Such attacks usually lead to a server overload. Computers exposed to DoS attacks usually need to be restarted in order to work properly.


----------


#### Port scanning

Port scanning is used to determine which computer ports are open on a network host. A port scanner is software designed to find such ports.

A computer port is a virtual point which handles incoming and outgoing data – this is crucial from a security point of view. In a large network, the information gathered by port scanners may help to identify potential vulnerabilities. Such use is legitimate.

Still, port scanning is often used by hackers attempting to compromise security. Their first step is to send packets to each port. Depending on the response type, it is possible to determine which ports are in use. The scanning itself causes no damage, but be aware that this activity can reveal potential vulnerabilities and allow attackers to take control of remote computers.

Network administrators are advised to block all unused ports and protect those that are in use from unauthorized access.



----------


#### SMB Relay

SMBRelay and SMBRelay2 are special programs that are capable of carrying out attacks against remote computers. The programs take advantage of the Server Message Block file sharing protocol which is layered into NetBIOS. A user sharing any folder or directory within the LAN most likely uses this file sharing protocol. Within local network communication, password hashes are exchanged.

SMBRelay receives a connection on UDP port 139 and 445, relays the packets exchanged by the client and server, and modifies them. After connecting and authenticating, the client is disconnected. SMBRelay creates a new virtual IP address. The new address can be accessed using the command “net use \\192.168.1.1“. The address can then be used by any of the Windows networking functions. SMBRelay relays SMB protocol communication except for negotiation and authentication. Remote attackers can use the IP address as long as the client computer is connected.

SMBRelay2 works on the same principle as SMBRelay, except it uses NetBIOS names rather than IP addresses. Both can carry out “man-in-the-middle” attacks. These attacks allow remote attackers to read, insert and modify messages exchanged between two communication endpoints without being noticed. Computers exposed to such attacks often stop responding or restart unexpectedly. To avoid attacks we recommend that you use authentication passwords or keys.


----------


> You mention that some users use a VPN to avoid revealing their IP address to Twitter. In this case, a VPN would still prevent the user's IP address from being revealed via the method described in your report.

Users use a VPN to avoid IP-Address tracking, a VPN wouldn't prevent the user's IP  address from being revealed via the method described in the report but will make the targeting harder a bit.

---

2017-02-18 11:01:57 +0000: @xc0d3rz (comment)
Here I have the request headers log for a request logging service called **"RequesBin"**

![F161812](https://cdn.rawgit.com/xc0d3rz/xc0d3rz.github.io/e28f515e/img/Screenshot_at_2017-02-18_13_59_43.png)

---

2017-02-21 16:39:13 +0000: @xc0d3rz (comment)
Also a similar bug was reported to Facebook in the past year (2016) and have been accepted and reward

---

2017-02-23 02:23:59 +0000: @bugtriage-jason (bug not applicable)
Thank you for the followup.

We appreciate you taking the time to submit this to us. However, we do not believe this poses a security risk as the content is clearly marked as being hosted by a 3rd party and thus they will have access to the user's IP address.  While we will be closing this report, we encourage you to continue searching for vulnerabilities.

Thank you for thinking of Twitter security.

---

