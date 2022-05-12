# Penetration Testing

**There are 6 phases to penetration testing:**

1. Mission Definition
2. Recon
3. Footprinting
4. Exploitation and initial access
5. Post exploitation
6. Document Mission


#### Penetration Test Reporting

Two types of notes: **Operation Notes** vs **Formalized Reporting**

Two types of summaries: *Executive Summary* vs *Technical Summary*


# Exploit Databases

[National Vulnerability Database](https://nvd.nist.gov/vuln/search)

^ This robust database from the National Institute of Standards and Technology from the US Department of Commerce contains a search function for CVE's

[CVE Index List](http://cve.mitre.org/cve/search_cve_list.html)

^ Essentially the same as the NVD

[Exploit Database](https://www.exploit-db.com/)

^ self explanatory, extremely helpful tool


# Scanning and Reconnaissance

#### Passive Recon

`cat /etc/os-release` 	provides OS info

`lsb_release -a`  	provides OS info

`uname -a`  	print system information

`hostnamectl` 	print system information in a cleaned up format

`ip addr` print IP addresses with interfaces


#### Ping Sweep AKA Host Discovery

`for i in {1..254}; do (ping -c 1 x.x.x.$i | grep "bytes from" &) ;done`    #used for host discovery within the network


#### NMAP Scans

`nmap x.x.x.x` #will scan the common 1000 ports

`nmap x.x.x.x -p-` #will scan all 65535 ports

`nmap x.x.x.x -p <ports>` #will scan specified ports

`nmap -Pn -T5 x.x.x.x -p-` #will do fast (-T5) scan on all ports (-p-) without host discovery (-Pn). useful for proxychains

`nmap -Pn -T5 x.x.x.x -sV -p 22,3389` #similar to aboce; will do additional scanning on service version (-sV) and on only the specified ports


#### NMAP Scripting Engine

[NSE Website](https://nmap.org/book/man-nse.html)

`cd /usr/share/nmap/scripts`  #perform an `ls` and note how many scripts you have!

`nmap -Pn -T5 x.x.x.x --script=banner.nse -p 22,3389`    #runs the `banner.nse` script on the given IP address

`nmap -Pn -T5 x.x.x.x --script="discovery" -p 22,3389`  #runs all of the `discovery` type scripts on the given IP address


# Scraping Data

see web.py script in 'scripts' directory

[link to script](https://github.com/jacobcreedwatkins/security/blob/main/web.py)


1. Change the `authors` variable to match the HTML query you are matching, and set your `page` to the IP you are targeting through your dynamic tunnel
2. Alternatively, set to localhost:port if you are using a local port forwarding instead of a dynamic tunnel
3. Right click -> view page source allows you to see the HTML document, allowing you to query it with your script.


















======================================================================================================================================================================

										GUNNY NGUYEN's NOTES 
									

	cat /etc/os-release

	lsb_release -h
	lsb_release -a

	uname --help
	uname -a

	Kernel: 4.15.0-76-generic

	hostnamectl

	https://www.exploit-db.com/

	### Scanning & Recon



	https://twitter.com/hdrochon/status/1294260219850129420

	HTML Tags
	<head> </head>

	<div class="container"> </div>

	<small class="author" itemprop="author">Albert Einstein</small>

	sudo apt-get install python-lxml python-requests python3-pip -y

	nano web.py
	##### web.py #####
	import lxml.html
	import requests

	page = requests.get('http://quotes.toscrape.com')
	tree = lxml.html.fromstring(page.content)

	authors = tree.xpath('//small[@class="author"]/text()')

	print ('Authors: ',authors)
	##### web.py #####


	###Scanning
	1) Host Discovery (ping sweep)
	Bash Ping Sweep Command:
	for i in {1..254}; do (ping -c 1 192.168.1.$i | grep "bytes from" &); done


	for i in {1..254}; do (ping -c 1 192.168.65.$i | grep "bytes from" &); done

	https://www.rubyguides.com/2012/02/cli-ninja-ping-sweep/ 

	64 bytes from 192.168.65.1: icmp_seq=1 ttl=64 time=2.24 ms
	64 bytes from 192.168.65.3: icmp_seq=1 ttl=64 time=2.68 ms
	64 bytes from 192.168.65.10: icmp_seq=1 ttl=128 time=1.07 ms
	64 bytes from 192.168.65.20: icmp_seq=1 ttl=64 time=0.017 ms
	64 bytes from 192.168.65.30: icmp_seq=1 ttl=64 time=0.993 ms

	2) Host Enumeration (Port Scanning)


	nc 
	nmap X.X.X.X	#Scan the common 1000 ports
	nmap X.X.X.X -p- #Scan all 65535 ports
	nmap X.X.X.X -p 22 #Scan specified ports


	nmap 192.168.65.10 -p- #With host discovery
	nmap -Pn 192.168.65.10 -p- #Without host discovery
	nmap -Pn -T5 192.168.65.10 -p- #With T5 timing template

	PORT      STATE SERVICE
	22/tcp    open  ssh
	135/tcp   open  msrpc
	139/tcp   open  netbios-ssn
	445/tcp   open  microsoft-ds
	3389/tcp  open  ms-wbt-server
	5040/tcp  open  unknown
	5357/tcp  open  wsdapi
	5800/tcp  open  vnc-http
	5900/tcp  open  vnc
	5985/tcp  open  wsman
	5986/tcp  open  wsmans
	47001/tcp open  winrm
	49664/tcp open  unknown
	49665/tcp open  unknown
	49666/tcp open  unknown
	49667/tcp open  unknown
	49668/tcp open  unknown
	49670/tcp open  unknown
	49671/tcp open  unknown
	49672/tcp open  unknown


	3) Host Interrogation (Service Interrogation)

	nmap -Pn -T5 192.168.65.10 -sV -p 22,3389

	PORT     STATE SERVICE       VERSION
	22/tcp   open  ssh           OpenSSH for_Windows_8.1 (protocol 2.0)
	3389/tcp open  ms-wbt-server Microsoft Terminal Services



	#NMAP NSE

	NMAP NSE:
	https://nmap.org/book/man-nse.html

	Script Categories:
	https://nmap.org/book/nse-usage.html#nse-categories


	NMAP NSE Scripts Location:
	cd /usr/share/nmap/scripts/

	banner.nse

	cat banner.nse | less

	categories = {"discovery", "safe"}

	nmap -Pn -T5 192.168.65.10 --script=banner.nse -p 22,3389

	PORT     STATE SERVICE
	22/tcp   open  ssh
	|_banner: SSH-2.0-OpenSSH_for_Windows_8.1
	3389/tcp open  ms-wbt-server

	nmap -Pn -T5 192.168.65.10 --script="discovery" -p 22,3389

	cat tso-enum.nse | grep cate

	NSE Scripts:
	https://nmap.org/nsedoc/scripts/
