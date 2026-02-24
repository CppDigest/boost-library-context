# #547 - Computing the square of a number [Open]

> Username: afabri  
> Created at: 2023-04-06 12:48:01 UTC  
> Updated at: 2023-04-06 16:06:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/547  

I am wondering if there is a function for computing the square of an integer.  I ask because GMP has [` mpn_sqr()` ](https://gmplib.org/manual/Low_002dlevel-Functions).

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-04-06 16:06:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/547#issuecomment-1499302630  

If you have a large number of limbs, you can improve efficiencs by computing only half the triad of coefficients and doubling them - or something like that. I believe this reduces the computational complexity.  
  
Every time that i try for it i miss something and multiprecision fails. but I#m really prone to typos and mistakes.  
  
I would almost want to take this on in a GSoC 24 with some other optimizations unless it gets done sooner.  
  
On a practical note, it is a bit harder to see the clean C++ interface to catch many use cases. You could provide a squaring functoin and clients knowing they need a square could use it. internally i _think_ you could see if `&left == &right` in `left * right = result`, but I'm not sure if that is robust enough.
