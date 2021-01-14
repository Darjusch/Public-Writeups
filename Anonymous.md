# This is a Writeup for the Room Anonymous on http://tryhackme.com 

## Enumeration

####Nmap

First we use nmap to see which ports are open and which services are running on them.
[![https://imgur.com/3KHpnMu.png](https://imgur.com/3KHpnMu.png)](https://imgur.com/3KHpnMu.png)

The first thing that catches our eye is the possibility to log into FTP anonymously which also hints to the room name so lets try that.
[![https://imgur.com/NSv0ZJ1.png](https://imgur.com/NSv0ZJ1.png)](https://imgur.com/NSv0ZJ1.png)

Lets discover which files we can find on the ftp server:
[![https://imgur.com/IyxL5jx.png](https://imgur.com/IyxL5jx.png)](https://imgur.com/IyxL5jx.png)
and use the get command to download the files.

The only interesting file we found is this bash script:
![Imgur](https://i.imgur.com/FS6sPPw.png) 
i dont know yet how to exploit it but i am sure we will find out later on.

Lets try to enumerate the SMB shares on Port 139 and 445

For this we can use the tool Enum4Linux which is pretty handy.

The usage is straight forward: 
enum4linux targetIP

We found a SMB share called pics.
![Imgur](https://i.imgur.com/ayu97kS.png)

Lets try to loginto that with the smbclient.


####Gobuster

## Persistence

## Privilege-escalation



