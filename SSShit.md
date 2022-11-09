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

Because $ys_0 = input$, we can choose $ys_0$ to be whatever we want it to be. In addition, we know every variable here except for p. Thus the goal is to find a pathway from $ys_0$ to m without p actually mattering. Since p only exists as %p, we can construct a narrow set of assumptions to exclude p from having any real effect.

Assumption One: $ys_0 * 3 < p$

Thus we can eliminate the %p to get $intercept_0 = ys_0*3$

Then we can substitute and distibute to get $intercept_1 = (ys_1 * p - ys_1 * 3 + ys_0 * 3)%p $

Using the following rule $(a+b)%p = (a%p + b%p)%p$ we get:

$intercept_1 = ((ys_1 * p)%p + (-ys_1 * 3)%p + (ys_0 * 3)%p) % p$

$(ys_1 * p)%p = 0 and (ys_0 * 3) % p = ys_0 * 3$ (based on Assumption 1) so we get:

$intercept_1 = (0 + (-ys_1 * 3)%p + ys_0 * 3 )%p

Now that middle statement looks like it could give us trouble, but in fact there is a useful trick around it. Some of you may be wondering why $a % b$ is being used and not $a \mod b$. The answer is that Python's % operator is not exactly the same as the mathematical modulus. In fact, in this specific instance, it is quite different. When $|a| < b$ and $a<0$, $a \mod b = b + a$ in Python world. Therefore we end up with: 

$intercept_1 = (p - ys_1 * 3 + ys_0 * 3 )%p$

Group together $ys_1 * 3 + ys_0 * 3$. We can see that if this statement is negative, the % p operation will be meaningless. That leads us to:

Assumption Two: $ys_1 > ys_0$

$intercept_1 = p - ys_1 * 3 + ys_0 * 3$

Now we substitute again

$intercept_2 = (ys_2 + p - ys_1 * 3 + ys_0 * 3)%p$

Immediately we can see that we are going to have some troubles here, so we employ 

Assumption Three: $ys_2 + ys_0 * 3 - ys_1 * 3 < p $

On its surface, Assumption Three looks as if it could be untrue (or at least difficult to ensure). But remember that by Assumption Two, we get $ys_0 - ys_1 <0 $.  Applying that here makes it clear that the actual assumption being made is that $ys_2 < p$

Simplifying and substituting we get $m = ys_2 + ys_0 * 3 - ys_1 * 3$ Rearrange to get the final equation:

$\frac{m-ys_2 + ys_1 * 3}{3} = ys_0$

Now make an important note here: 

The assumptions made here are **insufficient** for all the possible formulations. If $ys_2 > p $, for example, we would find that our formula doesn't work. If $ys_1$ is too large, $ys_3$ will need to be a **negative** number, forbidden by the scope of the problem. Therefore the script needs to be run several times until a correct result can be derived. 

# My Process
