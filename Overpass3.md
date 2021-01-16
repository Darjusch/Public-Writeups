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

## Privilege Escalation

Lets find out what we can execute and how we can escalate our privileges.








