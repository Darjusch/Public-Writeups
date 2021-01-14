# THM---Ignite-Writeup

This is a write up for the ROOM Ignite on THM.

First i scan the machine with nmap and while nmap is running i will go check manually for somehints on the webpage.
![CMS VERSION 1.4](https://i.imgur.com/FovmUTc.png)

![Imgur](https://i.imgur.com/OzfJMPd.png)
We successfully logged in with the credentials ! 
Lets try to find a way to upload a reverseshell! 
![Imgur](https://i.imgur.com/oGBUi1z.png)

Doesnt work..

Lets go back to the information we had about the CMS version and try to exploit that!

searchsploit is our friend
![Imgur](https://i.imgur.com/qC7nxMG.png)


we can download it directly with the command

searchsploit -m linux/webapps/47138.py

Open it with a editor of your choice and edit the ip and port
![Imgur](https://i.imgur.com/fTNFAHX.png)

I like to rename it to a more understandable name:
![Imgur](https://i.imgur.com/DGDwwtG.png)
and then run it

We got remote code execution! 
![Imgur](https://i.imgur.com/x33XOWc.png)

Lets improve our persistence! 

We create a nc listener on our host machine
![Imgur](https://i.imgur.com/0c8XT9R.png)

and then get a reverseshell on the targetmachine 
![Imgur](https://i.imgur.com/azU0D0W.png)

It worked!
![Imgur](https://i.imgur.com/RfyyPXy.png)


now we can upgrade our shell using python
![Imgur](https://i.imgur.com/aI4vX5q.png)

We find the first flag
![Imgur](https://i.imgur.com/FqxcePh.png)

After not finding any SUID or SUDO commands to escalte my privileges i decided to check some config files.
For CMS systems we should always take a look at the config files and check if we might find a password or two.
![Imgur](https://i.imgur.com/UIXXYP7.png)


There is a lot of files:
![Imgur](https://i.imgur.com/aYz5OIF.png)

So instead of checking each file manually we can just use a neat little command to filter for the word pass in each of these files.
![Imgur](https://i.imgur.com/OmBXpSZ.png)


Now we can use the su command and switch to root!

Thank you for reading :) 

