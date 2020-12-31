---
title: TryHackMe - Bounty Hacker
published: true
---

# [](#header-1)TryHackMe room write-up: Bounty hacker

Before we roll over to the new year, I wanted to warm up with the TryHackMe room "Bounty Hacker" (The mid-west could be a little bit warmer right now). Let's do our best impression of Edward Wong Hau Pepelu Tivrusky IV and get to hacking.
<br/>
![](/soWatt.github.io/images/THM_BH/THM_BH1.PNG)
<br/>
Before proving our "elite hacker skills" we have to deploy the machine. After the machine is deployed, we can begin with recon and port scanning.
<br/>
## RECONNAISSANCE
I first ran a nmap scan to see what ports were open and also made an attempt to detect service/version info.
```
nmap -sV 10.10.248.199
```
The scan returned the following results:
![](/soWatt.github.io/images/THM_BH/THM_BH2.PNG)
I started by looking at what the webserver was serving up. There was the following webpage:
![](/soWatt.github.io/images/THM_BH/THM_BH3.PNG)
Now that's some html straight out of the 90's, it fits very well with the Cowboy Bebop theme. Other than the promise of bell peppers and beef, there wasn't anything that really stood out on the webpage. My first thought was to bruteforce directories so I fired up gobuster.
```
gobuster dir -u http://10.10.248.199/ -w /usr/share/wordlists/rockyou.txt
```
Unfortunately, no directories were returned that weren't a dead end. Since there isn't much to interact with on the webpage, the next thing to take a look at would be the ftp server.

## The FTP Server
![](/soWatt.github.io/images/THM_BH/THM_BH4.PNG)
Well look at that, it's an anonymous ftp server. I reconnected to the ftp server again:
![](/soWatt.github.io/images/THM_BH/THM_BH5.PNG)
And was able to find two text files: locks.txt and task.txt. I transfered a copy of each to my local machine. After looking at the contents of the tasks.txt, I had the answer to the question 3.
![](/soWatt.github.io/images/THM_BH/THM_BH6.PNG)
As for locks.txt, it looked like a obvious password list. Seeing as there is no login for the webpage and the ftp server is anonymous, the only thing left is ssh. I decided to use hydra to try and see if I could get a connection using the username "lin".
![](/soWatt.github.io/images/THM_BH/THM_BH7.PNG)
Just like that we now are on the box as Lin.
![](/soWatt.github.io/images/THM_BH/THM_BH8.PNG)
The user.txt file was in that directory, I'll let you find out for yourself what the flag is ;). The final step in this room is some good old fashioned Linux PrivEsc.
## Privelage Escalation
My usual first step for escalating privelages to root on a linux box is to check what the current user can run using sudo (if they can).
![](/soWatt.github.io/images/THM_BH/THM_BH9.PNG)
So Lin can run tar as super user, that might work! Heading over to gtfobins.github.io I ran a search for tar and found this:
![](/soWatt.github.io/images/THM_BH/THM_BH10.png)
```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
And just like that we got root.
![](/soWatt.github.io/images/THM_BH/THM_BH11.PNG)
<br/>
Even though the room is complete since I can read the root.txt flag, I wanted to see if I could come up with any other ways to escelate privelages. I ran linpeas on the machine and saw that the PATH variable had something interesting:
![](/soWatt.github.io/images/THM_BH/THM_BH12.PNG)
Since I couldn't really find any cronjobs that were running as root I could use to abuse the writable path locations, I ended up giving up on that option. I'm going to go read up on some privEsc material, thanks for reading the write-up!
