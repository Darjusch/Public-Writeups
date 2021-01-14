# THM Ignite-Writeup

This is a write up for the room Ignite on THM.

First i start my scans with nmap and gobuster, while these are running i go and explore the page manually.
Already on the frontpage we are greeted with the version of the CMS 
![CMS VERSION 1.4](https://i.imgur.com/FovmUTc.png)
And some admin credentials, ets try them out!
![Imgur](https://i.imgur.com/OzfJMPd.png)
We successfully loggedin with the credentials ! 
This was easy lets try to find a way to upload a reverseshell! 
![Imgur](https://i.imgur.com/oGBUi1z.png)
After some attempts of trying to by pass some possible filters to upload a reverseshell with out much success i tried to go a different route first.
Lets go back to the information we had about the CMS version and try to exploit that!

Searchsploit is our friend
![Imgur](https://i.imgur.com/qC7nxMG.png)

we can download it directly with the flag -m

searchsploit -m linux/webapps/47138.py

Open it with a editor of your choice and edit the ip and port to the one of your target machine
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

