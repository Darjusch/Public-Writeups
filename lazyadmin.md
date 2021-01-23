Writeup for LazyAdmin on https://tryhackme.com


First we add the target ip to our /etc/hosts.

sudo vim /etc/hosts 

add the ip and then the name.

[![https://imgur.com/EkTTYEj.png](https://imgur.com/EkTTYEj.png)](https://imgur.com/EkTTYEj.png)

Now we can start the enumeration with nmap:

I like to start with a quick synscan because it is fast and we can then manually proceed while we let a full port scan run.

[![https://imgur.com/ZrMaaDn.png](https://imgur.com/ZrMaaDn.png)](https://imgur.com/ZrMaaDn.png)

So we can see there is ssh and http open.
SSH doesnt help us a lot right now so lets start a gobuster on http.

[![https://imgur.com/mA0wvxo.png](https://imgur.com/mA0wvxo.png)](https://imgur.com/mA0wvxo.png)

We can see a /content endpoint so lets visit that.

On the page we can see that the Website Management System they used is SweetRice.

[![https://imgur.com/7wj6dH3.png](https://imgur.com/7wj6dH3.png)](https://imgur.com/7wj6dH3.png)

We can also see a link which leads us to a page saying what we should do once the website is installed.
Since the admin is lazy lets see if we can find something there.

The second field talks about storing important data into one specific directory:

[![https://imgur.com/KWaNL50.png](https://imgur.com/KWaNL50.png)](https://imgur.com/KWaNL50.png)

Lets try to access it:

[![https://imgur.com/zdMfMUh.png](https://imgur.com/zdMfMUh.png)](https://imgur.com/zdMfMUh.png)

Okay that worked.

There we find a filename latest.txt when we open it we see a version number 1.5.1 

Lets search for a exploit of sweetRice with the Version 1.5.1.

On Exploit-DB i find a exploit:

SweetRice 1.5.1 - Arbitrary File Upload

Lets try that out!

I downloaded ,renamed it and then made it executable.

[![https://imgur.com/FIkrCrb.png](https://imgur.com/FIkrCrb.png)](https://imgur.com/FIkrCrb.png)

I didnt get this one to work, while i did this i let dirbuster run so i can also have some reccursive search going on and i found a database backup.

[![https://imgur.com/fZQd3MZ.png](https://imgur.com/fZQd3MZ.png)](https://imgur.com/fZQd3MZ.png)

Lets have a look.

Lets read all human readable strings from the file and grep for some interisting things:

[![https://imgur.com/ThVeFDq.png](https://imgur.com/ThVeFDq.png)](https://imgur.com/ThVeFDq.png)

Looks like we found a password hash! 

Lets crack it with johntheripper.

Just add the hash we found into a file remove the \\ and then specify a wordlist: 
john hash.txt -w /usr/share/wordlists/rockyou.txt 

Now that we have a password and a username(manager)!

Lets try to ssh in!

Hmm this doesn't work.. 
So what are the credentials for? 

I looked back at the dirbuster scan and found a loginpage: /content/as/.

It works! 

From other boxes i learned already that the plugin section is always a good place to test reverseshells! 
Lets try a php reverseshell from pentestmonkey!

This didn't work for me but i managed to upload it in the backup section.

[![https://imgur.com/s8P5IK2.png](https://imgur.com/s8P5IK2.png)](https://imgur.com/s8P5IK2.png)

Now i visited the site where the shell was uploaded to and i got my reverseshell!

[![https://imgur.com/IAsk7bU.png](https://imgur.com/IAsk7bU.png)](https://imgur.com/IAsk7bU.png)

In the homedirectory we find a user itguys and the flag:

[![https://imgur.com/c6FS0Hf.png](https://imgur.com/c6FS0Hf.png)](https://imgur.com/c6FS0Hf.png)

First i looked for SUID's, GUID's and Cronjobs with out much success but simply checking sudo -l shows us a way:

[![https://imgur.com/bJITiPP.png](https://imgur.com/bJITiPP.png)](https://imgur.com/bJITiPP.png)

If we look at /etc/copy.sh we can see that there is already a reverseshell we just have to change the ip and port, create a nc listener and run that with root privilege.

[![https://imgur.com/hShfqWk.png](https://imgur.com/hShfqWk.png)](https://imgur.com/hShfqWk.png)

We made it! 

Thank you for reading :) 




 


