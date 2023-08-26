---
title: First HTB Machines 
published: true
---
<br/>
# [](#header-1) Four days, Four easy machines on Hack the Box
<br/>
Over the past week, I've finally started working on the Hack the Box with some easy machines. Here are my notes:
<br/>
<br/>
## [](#header-2) Machine 1: Keeper
<br/>
The Keeper box was the easiest of the four, I added an entry to /etc/hosts so keeper.htb would resolve correctly.
<br/>
The web application running out of the box was a specific ticketing system. Searching the internet for the default login creds for that system (root/Password) was the solution to gaining access to the application. In the ticketing section there was an entry by a user named "lnorgaard" and an associated password of "Welcome2023!". This allowed acccess to the Machine via SSH.
<br/>
After reading the user flag, there was a zip folder called RT3000.zip. The extracted contents included a dump file of the keepass process on a windows host and the keepass database file which stored the creds. There is a vulnerability with keepass 2.54 which allows a user to extract the password: [https://nvd.nist.gov/vuln/detail/CVE-2023-32784](CVE-2023-32784). After running a POC exploit of this vulnerability that I found on Github, the password associated with the kdbx file was "#ødgrød Med Fløde" where the "#" character could be any letter/symbol/number (the exploit does not display the first character). A quick google search of the string will lead to a porridge dish called "Rødgrød Med Fløde" which was the password to the keepass database. This password database contained the root accounts ssh key + password and was used to get root on the box.
<br/>
<br/>
## [](#header-2) Machine 2: Sau
<br/>
<br/>
I found Sau to also be a less challenging machine although a little more difficult than Keeper. Nmap detected 3 services running on the server: ssh on port 22, a web server that was inaccessible from outside traffic on 80, and another application on port 55555. The application found on 55555 was a vulnerable verson of "requests-baskets", a go based web app that system that collects web based requests and stores them in accessible urls. Using an SSRF, it was posible to view the other web application that was on 80 which was hosting a version of Maltrail that was vulnerable to RCE. Chaining these two vulnerabilities together to get a reverse shell resulted in getting the user flag. The unprivelaged user on the node had the ability to run a sudo command that would check the status of the maltrail service. After executing this command as super user, it was possible to invoke the pager and gain a shell with root privelages.
<br/>
<br/>
## [](#header-2)Machine 3: Pilgrimage (The point I started to struggle a bit)
<br/>
<br/>
Pilgrimage was more difficult for me, an nmap scan with service enumeration exposed a .git repository found within the web app. I wasn't sure exactly how to pull down that repository but was directed to a tool called "git-dumper". git-dumper successfully pulled the application code which was crucial for understanding the vulnerability of the web app. The application would allow a user to upload a .png or .jpg file and the server would shrink the photo by 50% then display it back to the user. The associated code found in the git repo showed that it accomplished this by using an imagemagick convert command. By crafting a malformed png file, it was possible to read files on the server in the returned png's meta data (CVE-2022-44268). Using this allowed the user to read /etc/password to find the user "emily" and a sqlite database file (which the file path was also found in the source code from the git repository) which contained a password in the clear for the emily account. Now having access to the user, gaining root privilages involved viewing the processes running on the server. A process called "malwarescan.sh" was looking for certain files and the running binwalk on the file. This version of binwalk was vulnerable to RCE (2.3.2) and could be used to get a reverse shell as root. Note to self: try git-dumper next time nmap shows a git repo in the web app, before this I never heard of this tool.
<br/>
<br/>
## [](#header-2)Machine 4: Topology (The point I started to struggle a lot)
<br/>
<br/>
Topology was a really cool box but I crawled through it at a snails pace and needed a lot hints. Topology was running a web server that had a webpage containing a fake college department of mathematics page. There was a link to a project that allowed a user to submit latex code via a text box and the server would return an image of the result of the latex code. The root directory of the website some php files that shed some light on how the Latex (interpreter?) was configured. The biggest information gathered from this file was that it was set up with "restriced shell" mode which would make it unlikely that the application would allow straight system commands to pass for the server to run. This was the case as most commands would return an image with this text "illegal command detected". However, it was posible to read files on the server but using \lstinputlisting{path/to/file}. This was used to pull two files, the first was /etc/passwd which showed us a user named "vdaisley" (a name we also so in the latex apps php files) and an .htapasswd file that was found in /var/www/dev/. The hash in the htapasswd file was easily cracked and the password for the vdaisley user was calculus20. After reading the user flag, there was a root owned directory that was world writeable (but not readable) in opt for the gnuplot software. By putting a .plt file with a system command then it was possible to get a reverse shell as root. Note to self: do some more research on languages I have zero experience with like latex.
<br/>
<br/>

Thanks for stopping by and have a great day :)