---
title: TryHackMe - Bounty Hacker
published: true
---

# [](#header-1)TryHackMe room write-up: Bounty hacker

Before we roll over to the new year, I wanted to warm up with the TryHackMe room "Bounty Hacker" (The mid-west could be a little bit warmer right now). Let's do our best impression of Edward Wong Hau Pepelu Tivrusky IV and get to hacking.
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH1.PNG)
<br/>
<br/>
<br/>
Before proving our "elite hacker skills" we have to deploy the machine. After the machine is deployed, we can begin with recon and port scanning.
<br/>
<br/>
<br/>
## RECONNAISSANCE
<br/>
I first ran a nmap scan to see what ports were open and also made an attempt to detect service/version info.
<br/>
```
nmap -sV 10.10.248.199
```
The scan returned the following results:
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH2.PNG)
<br/>
I started by looking at what the webserver was serving up. There was the following webpage:
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH3.PNG)
<br/>
<br/>
Now that's some html straight out of the 90's, it fits very well with the Cowboy Bebop theme. Other than the promise of bell peppers and beef, there wasn't anything that really stood out on the webpage. My first thought was to bruteforce directories so I fired up gobuster.
<br/>
```
gobuster dir -u http://10.10.248.199/ -w /usr/share/wordlists/rockyou.txt
```
<br/>
<br/>
Unfortunately, no directories were returned that weren't a dead end. Since there isn't much to interact with on the webpage, the next thing to take a look at would be the ftp server.

<br/>
## The FTP Server
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH4.PNG)
<br/>
Well look at that, it's an anonymous ftp server. I reconnected to the ftp server again:
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH5.PNG)
<br/>
And was able to find two text files: locks.txt and task.txt. I transfered a copy of each to my local machine. After looking at the contents of the tasks.txt, I had the answer to the question 3.
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH6.PNG)
<br/>
As for locks.txt, it looked like a obvious password list. Seeing as there is no login for the webpage and the ftp server is anonymous, the only thing left is ssh. I decided to use hydra to try and see if I could get a connection using the username "lin".
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH7.PNG)
<br/>
Just like that we now are on the box as Lin.
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH8.PNG)
<br/>
The user.txt file was in that directory, I'll let you find out for yourself what the flag is ;). The final step in this room is some good old fashioned Linux PrivEsc.
<br/>
## Privelage Escalation
<br/>
My usual first step for escalating privelages to root on a linux box is to check what the current user can run using sudo (if they can).
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH9.PNG)
<br/>
So Lin can run tar as super user, that might work! Heading over to gtfobins.github.io I ran a search for tar and found this:
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH10.png)
<br/>
```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
<br/>
And just like that we got root.
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH11.PNG)
<br/>
<br/>
Even though the room is complete since I can read the root.txt flag, I wanted to see if I could come up with any other ways to escelate privelages. I ran linpeas on the machine and saw that the PATH variable had something interesting:
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH12.PNG)
<br/>
<br/>
Since I couldn't really find any cronjobs that were running as root I could use to abuse the writable path locations, I ended up giving up on that option. I'm going to go read up on some privEsc material, thanks for reading the write-up!


