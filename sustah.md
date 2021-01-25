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

If we recall the description of the box:

"The developers have added anti-cheat measures to their game. Are you able to defeat the restrictions to gain access to their internal CMS?"

Lets look at header injection by portswiggger:
https://portswigger.net/web-security/host-header/exploiting

[![https://imgur.com/kmPo0Wq.png](https://imgur.com/kmPo0Wq.png)](https://imgur.com/kmPo0Wq.png)




## Privescalation

