# THM---Ignite-Writeup

This is a write up for the ROOM Ignite on THM.

First i scan the machine with nmap and while nmap is running i will go check manually for somehints on the webpage.
![CMS VERSION 1.4](https://i.imgur.com/FovmUTc.png)

![Imgur](https://i.imgur.com/OzfJMPd.png)

Doesnt work..

Lets go back to the information we had about the CMS version and try to exploit that!

searchsploit is our friend



we can download it directly with the command

searchsploit -m linux/webapps/47138.py

Open it with a editor of your choice and edit the ip and port


I like to rename it to a more understandable name:

and then run it

We got remote code execution! 


Lets improve our persistence! 

We create a nc listener on our host machine


and then get a reverseshell on the targetmachine 





now we can upgrade our shell using python


We find the first flag


After not finding any SUID or SUDO commands to escalte my privileges i decided to check some config files.
For CMS systems we should always take a look at the config files and check if we might find a password or two.



There is a lot of files:


So instead of checking each file manually we can just use a neat little command to filter for the word pass in each of these files.



Now we can use the su command and switch to root!

Thank you for reading :) 

