---
title: Dumping Router Firmware on TryHackMe 
published: true
---
<br/>
# [](#header-1) Dumping router firmware and analysis
<br/>
<br/>
The "Dumping Router Firmware" room on tryhackme is a great one, I've always been curious what the inner workings of networking equipment are and this was a great introduction firmware analysis. Everyday I get closer and closer to hardware, I wonder if y'all do the same and naturally gravitate toward it too. 
<br/>
There were a few setup steps detailed at the start of the room:
<br/>
<br/>
![](images/THM_Router/1.png)
<br/>
<br/>
So the "jefferson" is a python tool that can be used to extract JFFS2 filesystems. It's currently an archived directory but a really neat tool. For the sake of keeping the room simple (Thank you to the room creators andrea526 and tryhackme) there is a github repo with the .img file we will be working with. I have no clue how to actually rip the image file from a router :). 
<br/>
We are then asked to check the integrity of the .img file against a sha256 hash:
<br/>
<br/>
![](images/THM_Router/2.png)
<br/>
<br/>
Hooray! We start by running strings on the img file, and notice a lot of the firmware image is not encrypted. We can get the answer to the first two questions by seeing the results of running strings on the img file:
<br/>
<br/>
![](images/THM_Router/3.png)
<br/>
<br/>
So it's a linksys router and it's running linux. Cool, now we use binwalk to look for file signatures. The following is the result:
<br/>
<br/>
![](images/THM_Router/4.png)
<br/>
<br/>
Hmmmmm yeah I know some of these words. We have info on the filesystem type, encoding, creation date, CPU architecture, and Cyclical Redundancy Check (CRC) information. The room gives more details on what a CRC's purpose is, which is to make sure that "the file contents were not corrupted or modified in transit". 
<br/>
Binwalk extracts 2 additional files and we are encouraged to run binwalk -e on the file called "6870". When we do so we get a series of files, running strings on 799E38.cpio shows us some weird messages: 
<br/>
<br/>
![](images/THM_Router/5.png)
<br/>
<br/>
Now it's time to take a look at the jffs2 file system. I had to switch to a VM because I was having issues doing this in WSL2. This took me a while, it's been forever since I mounted a file system:
<br/>
<br/>
![](images/THM_Router/6.png)
<br/>
<br/>
The room expands on what is found in this filesystem and I encourage anyone to take a look! It's pretty neat.
