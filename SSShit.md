#The Problem
SSShit was one of the most interesting challenges during buckeyeCTF and certainly a load of fun



#My Solution

So starting out, the two pieces to the algorithm are the encryption and decryption steps.
I struggled for several hours on this problem, attacking it from various angles (more on that below). 
My final solution involved targeting solely the decryption part of the algorithm, displayed below:
![decryption algorithm](https://github.com/wacky9/buckeyectfWriteups/IMG/SSShitDecryption.png)

Right away the first trick is determining what product is for each loop. I solved this lazily, simply adding a print statement and running the code several times.
The result I discovered is as follows:

$product_0 = 3$

$product_1 = p-3$

$product_2 = 1$


#My Process
