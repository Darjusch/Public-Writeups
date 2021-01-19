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

