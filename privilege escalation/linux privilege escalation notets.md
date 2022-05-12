[gtfo bins website](https://gtfobins.github.io)

- ignore anything about restricted shell
- literally copy paste the valid commands you find from performing `sudo -l
- super duper best friend for privesc`
- look at nonstandard binaries (gtfo bins wont help for these)
## SUDO STUFF

`sudo -l`	:	shows the commands you can run sudo with as current user. IF YOU SEE ANYTHING, TAKE NOTE!
`/etc/sudoers`	:	file containing all users with sudo access
- if you can sudo `cat`, you can crack passwords with john after looking into `/etc/shadow`!!
- world writeable directories: use `/tmp`

## SUID/GUID
- look for these set bits to pop root
- `find / -perm /4000 2>/dev/null`	:	returns any files with the SUID bit set
- `find / -perm /2000 2>/dev/null`	:	returns any files with the GUID bit set
- 

## CRONS
- [crontab guru, helpful for creating crons](https://crontab.guru/)
- look into netcat listeners, anything running under root, anything that looks out of the ordinary/opening weird files. use your analyst brain
- `crontab -l`    :   lists all cronjobs
- `sudo crontab -l`   :   lists all of root's cronjobs
- `/etc/crontab`      :   the actual crontab directory
- `/etc/cron.*`   :   dont actually use * here. there are 6 cron directories to check, start at cron.d and work your way through them
- look at crons! super important. netcat listeners and beacons are huge


## INSECURE PERMISSIONS / DOT IN THE PATH
- `echo $PATH`	:	shows your directory paths for binaries
- look for `.` in your path variable; can be exploited to run scripts with the same names as known binaries from your current workig directory


## USER ACCOUNTS

`user add`

## SANITIZING LOGS

- minimum of 2 logs (ssh login, ssh exit)
- syslog, rsyslog, /var/log

## READING FILES
-consider using head/tail to find flags easier


## RSYSLOG

`/etc/rsyslog.d/*`
`/etc/rsyslog.conf`





methodology

- look at sudo commands `sudo -l`
- if none? look at SUID/GUID bits with find command
- check gtfobins for each result from your find command related to SUID/GUID
- 


# JOHN THE RIPPER DEMO

```
sudo apt install john


vim shadow    #put the entire line into the file from /etc/shadow
john --wordlist=rockyou.txt shadow.txt --show 


                      ^ reconnaissance will show you what wordlist you need to use for john in order to pass le test






```






# CTFD

```
Donovian Linux Privilege Escalation, and Persistence (DLP)
XX Feb 2027
Start Time: 1300
Duration: 4 hours

Type of Operation: Cyberspace Exploitation (C-E)

Objective: Maneuver into the Donovian internal network, gain privileged access to discovered Linux hosts.

Tools/Techniques: SSH masquerade into internal network with provided credentials. Ports in use will be dependent on target location and are subject to change. Linux techniques to gain privileged access and persist are limited to host misconfigurations, open suid/sgid, weak permissions, and path. Network scanning tools/technique usage is at the discretion of student.

Scenario Credentials: FLAG = H@RDl1nux5t@rt0F@ct1v1ty

Prior Approvals: Privilege escalation, persistence, and restarting of services through host reboot. Host survey and log sanitation utilizing native command shells, which shell is at discretion of student. NOT authorized is uploading of tools or altering account information.

Scheme of Maneuver:
>internet_grey_host
->Pivot:192.168.28.105
--->T1: 192.168.28.27
--->T2: 192.168.28.12

Target Section:

Pivot
Hostname: Donovian-Terminal
IP: 192.168.28.105
OS: Ubuntu 18.04
Creds: comrade :: StudentReconPassword
Last Known SSH Port: 2222
PSP: rkhunter
Malware: none
Action: Perform SSH masquerade and redirect to the next target. No survey required, cohabitation with known PSP approved.

T1
Hostname: unknown
IP: 192.168.28.27
ghjcnbnenrf      (zeus)
OS: Linux ver: Unknown
Creds: comrade :: StudentPrivPassword
Last Known Ports: unknown
SSH PORT: 22 default
PSP: unknown
Malware: unknown
Action: Test supplied credentials, if possible gain access to host. Conduct host survey and gain privileged access.

T2
Hostname: unknown
IP: 192.168.28.12
OS: Linux ver: Unknown
Creds: comrade :: StudentPrivPassword
Last Known Ports: unknown
PSP: unknown
Malware: unknown
Action: Test supplied credentials, if possible gain access to host. Conduct host survey and gain privileged access.



USING A FALSE BINARY TO PERFORM PRIVESC (sussy ls command)

example:
> cd /tmp
> touch ls

#!/bin/bash
/bin/cat /home/billybob/f!@g1.txt > /tmp/actualFlag.txt
/bin/cat /home/billybob/10-million-password-list-top-10000.txt > /tmp/passList.txt
/bin/cat /home/billybob/logs > /tmp/logs
/usr/bin/sudo -l > /tmp/sudoCommands


change the commands accordingly


crontabs: 

> crontab -e

make sure you use the cron guru website to build your crons

append your command to the end of the crontab

watch -n1 'what_to_watch' ##for ease of use


```
