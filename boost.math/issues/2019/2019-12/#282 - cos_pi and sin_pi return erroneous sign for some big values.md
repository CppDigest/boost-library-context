# #282 - cos_pi and sin_pi return  erroneous sign for some big values [Open]

> Username: jtlap  
> Created at: 2019-12-19 22:27:13 UTC  
> Updated at: 2019-12-29 16:45:36 UTC  
> Url: https://github.com/boostorg/math/issues/282  

using boost 1.72  
in special functions part of boost math  
in file cos_pi.hpp lines 36 and 37:  
  
36   if(iconvert(rem, pol) & 1)  
37      invert = !invert;  
  
on the call cos_pi( 324812120769207.375) (for instance), rem is 324812120769207  
and iconvert(rem,pol) is -2147483648 which is certainly not expected as it changes the parity and so the invert boolean value is not changed and produces a wrong sign for the cosine.  
(cos_pi(1.375) is computed with the good sign because iconvert of 1 is 1)   
  
Possible correction:  
  
As the only important thing here is the parity of the flint value rem, I suggest to replace the test line 36 by  
  
36   if( floor(rem/2)*2 != rem)  
  
as it does not use any integral conversion  
  
related:  
  
sin_pi.h has exactly the same problem lines 41 and 42   
  
How I discovered the error:  
  
I wanted to use the function to test an simd implementation of mine of cospi and wondered why for some big values my result was the opposite. Using sollya I convinced myself that my result was the good one and found the bug in the quite easy to read implementation in boost math.  
  
I made the patch locally and it corrected all errors in my tests.  
This is perhaps also a problem to see in iconvert.  
    
Thank you for your very useful library.  
  
 J.T. Lapresté (jtlapreste@gmail.com)

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-12-29 15:31:56 UTC  
> Url: https://github.com/boostorg/math/issues/282#issuecomment-569515907  

@jtlap : Do you mind posting the diff from your patch or making a PR?  
  
Thanks!

---

## Comment 2

> Username: jtlap  
> Created at: 2019-12-29 15:44:42 UTC  
> Url: https://github.com/boostorg/math/issues/282#issuecomment-569516917  

ok, I have a pr ready, but I don't know how to push it as I have no login for 'https://github.com/boostorg/math.git/'

---

## Comment 3

> Username: jtlap  
> Created at: 2019-12-29 15:48:51 UTC  
> Url: https://github.com/boostorg/math/issues/282#issuecomment-569517224  

Is there any guest login available ?

---

## Comment 4

> Username: jtlap  
> Created at: 2019-12-29 15:54:48 UTC  
> Url: https://github.com/boostorg/math/issues/282#issuecomment-569517725  

however here is a diff result:  
  
iff --git a/include/boost/math/special_functions/cos_pi.hpp b/include/boost/math/special_functions/cos_pi.hpp  
index c565ac08b..d395d262b 100644  
--- a/include/boost/math/special_functions/cos_pi.hpp  
+++ b/include/boost/math/special_functions/cos_pi.hpp  
@@ -33,7 +33,7 @@ T cos_pi_imp(T x, const Policy& pol)  
       x = -x;  
    }  
    T rem = floor(x);  
-   if(iconvert(rem, pol) & 1)  
+   if(floor(rem/2)*2 != rem)  
       invert = !invert;  
    rem = x - rem;  
    if(rem > 0.5f)  
diff --git a/include/boost/math/special_functions/sin_pi.hpp b/include/boost/math/special_functions/sin_pi.hpp  
index c55c4ff79..cf4fff9f4 100644  
--- a/include/boost/math/special_functions/sin_pi.hpp  
+++ b/include/boost/math/special_functions/sin_pi.hpp  
@@ -38,7 +38,7 @@ inline T sin_pi_imp(T x, const Policy& pol)  
       invert = false;  
   
    T rem = floor(x);  
-   if(iconvert(rem, pol) & 1)  
+   if(floor(rem/2)*2 != rem)  
       invert = !invert;

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-12-29 16:36:43 UTC  
> Url: https://github.com/boostorg/math/issues/282#issuecomment-569521345  

@jtlap : You have to fork the repo, then the PR must be "compared across forks".  
  
Your diff is sufficient for me to get it working, but of course I'd like you to get a hang of the "fork->PR" workflow so we can continue benefiting from your fixes!

---

## Comment 6

> Username: jtlap  
> Created at: 2019-12-29 16:39:39 UTC  
> Url: https://github.com/boostorg/math/issues/282#issuecomment-569521553  

thank you, I will let you make the fix. I will fork "next time", if needed

---

## Comment 7

> Username: jtlap  
> Created at: 2019-12-29 16:45:36 UTC  
> Url: https://github.com/boostorg/math/issues/282#issuecomment-569522013  

Also, I will not investigate further, but I am quite sure there will be problems with iconvert
