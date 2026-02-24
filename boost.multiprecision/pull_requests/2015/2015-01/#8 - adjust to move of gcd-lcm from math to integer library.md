# #8 adjust to move of gcd/lcm from math to integer library [Merged]

> Username: jhunold  
> Created at: 2015-01-23 17:03:40 UTC  
> Updated at: 2015-01-23 19:51:51 UTC  
> Merged at: 2015-01-23 19:39:30 UTC  
> Closed at: 2015-01-23 19:39:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/8  

The last changes, I hope.

---

## Comment 1

> Username: pabristow  
> Created_at: 2015-01-23 18:03:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/8#issuecomment-71235579  

From: Jürgen Hunold [mailto:notifications@github.com]   
Sent: 23 January 2015 17:04  
To: boostorg/multiprecision  
Subject: [multiprecision] adjust to move of gcd/lcm from math to integer library (#8)  
  
The last changes, I hope.  
  
Well maybe in Boost code – I wonder how many in users code?  
  
Looks good to me.  
  
I am changing the Boost.Integer documentation to highlight this breaking change, including reference to Boost.Math and Boost.Multiprecision where changes have been noticed, so far.  
  
Hope this change has some benefits ;-)  
  
Paul  
  
---  
  
Paul A. Bristow  
  
Prizet Farmhouse  
  
Kendal UK LA8 8AB  
  
+44 (0) 1539 561830  
  
---  
  
You can view, comment on, or merge this pull request online at:  
  
  https://github.com/boostorg/multiprecision/pull/8  
  
Commit Summary  
-   adjust to move of gcd/lcm from math to integer library  
  
File Changes  
-   M include/boost/multiprecision/cpp_int/misc.hpp https://github.com/boostorg/multiprecision/pull/8/files#diff-0  (4)   
  
Patch Links:  
-   https://github.com/boostorg/multiprecision/pull/8.patch  
-   https://github.com/boostorg/multiprecision/pull/8.diff  
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/multiprecision/pull/8 .  https://github.com/notifications/beacon/ABBuAUKA5Bv9OlZAqVIVF79gDMM9vaO7ks5nknZtgaJpZM4DWbtl.gif

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-01-23 18:52:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/8#issuecomment-71244010  

Um - wasn't this fixed already in your last PR?

---

## Comment 3

> Username: jhunold  
> Created_at: 2015-01-23 19:04:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/8#issuecomment-71246146  

Oh, that is why I've had this deja-vu feeling.  
It seems you accidentally reverted this in  
 92748f63272088e6d97351ab49f4467638e6c713

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-01-23 19:39:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/8#issuecomment-71252062  

How the blazes did I manage that?  Apologies!

---
