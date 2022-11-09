# The Problem
SSShit was one of the most interesting challenges during buckeyeCTF and certainly a load of fun

I've split this writeup between a basic summary of what I did, and a longer explanation of the explorations, theories, and various other dead-ends I embarked upon trying to solve this problem. 

# My Solution

So starting out, the two pieces to the algorithm are the encryption and decryption steps.
I struggled for several hours on this problem, attacking it from various angles (more on that below). 
My final solution involved targeting solely the decryption part of the algorithm, displayed below:
![decryption algorithm](https://github.com/wacky9/buckeyectfWriteups/IMG/SSShitDecryption.png)

The goal of this is to determine the proper value for ys[0] **without** ever finding a definitive value for p.
The first step is determining what product is for each loop. The result I discovered is as follows:

$product_0 = 3$

$product_1 = p-3$

$product_2 = 1$

This eliminates the inner loop from analysis. Then I constructed a series of equations based on the outer loop

$intercept_0 = (ys_0 * product_0)%p$

$intercept_1 = (ys_1 * product_1 + intercept_0)%p$

$intercept_2 = (ys_2 * product_2 + intercept_1)%p$

$m = intercept_2$

Because $ys_0 = your{\_}input$, we can choose $ys_0$ to be whatever we want it to be. In addition, we know every variable here except for p. Thus the goal is to find a pathway from $ys_0$ to m without p actually mattering. Since p only exists as %p, we can construct a narrow set of assumptions to exclude p from having any real effect.

Assumption One: $ys_0 * 3 < p$

Thus we can eliminate the %p to get $y{\_}int_0 = ys_0*3$

Then we can substitute and distibute to get $y{\_}int_1 = (ys_1 * p - ys_1 * 3 + ys_0 * 3)%p $

Using the following rule $(a+b)%p = (a%p + b%p)%p$ we get:

$y{\_}int_1 = ((ys_1 * p)%p + (-ys_1 * 3)%p + (ys_0 * 3)%p) % p$

$(ys_1 * p)%p = 0 and (ys_0 * 3) % p = ys_0 * 3$ (based on Assumption 1)

# My Process
