# #6 Support for gcd and lcm of rational numbers. [Merged]

> Username: mlang  
> Created at: 2014-11-18 00:48:39 UTC  
> Updated at: 2015-08-27 16:57:37 UTC  
> Merged at: 2015-08-27 16:57:37 UTC  
> Closed at: 2015-08-27 16:57:37 UTC  
> Url: https://github.com/boostorg/rational/pull/6  



---

## Comment 1

> Username: pdimov  
> Created_at: 2015-01-20 18:42:29 UTC  
> Url: https://github.com/boostorg/rational/pull/6#issuecomment-70707943  

This should probably be updated now that gcd/lcm are in Integer.

---

## Comment 2

> Username: mclow  
> Created_at: 2015-04-02 02:39:10 UTC  
> Url: https://github.com/boostorg/rational/pull/6#issuecomment-88705265  

I like this, but I'm not seeing any tests.

---

## Comment 3

> Username: mlang  
> Created_at: 2015-04-02 10:10:09 UTC  
> Url: https://github.com/boostorg/rational/pull/6#issuecomment-88856949  

Marshall Clow notifications@github.com writes:  
  
> I like this, but I'm not seeing any tests.  
  
Mathematicians keep telling me that gcd on rational numbers is sort of  
undefined.  
Still, I have a use-case for it, and it behaves exactly as  
expected/required.  It is rather easy to drop these two specialisations  
in your own headers, but I think it would be more convenient if rational  
already provided it out of the box.  
  
##   
  
CYa,  
  ⡍⠁⠗⠊⠕

---

## Comment 4

> Username: mclow  
> Created_at: 2015-04-03 14:48:10 UTC  
> Url: https://github.com/boostorg/rational/pull/6#issuecomment-89309295  

> It is rather easy to drop these two specialisations  
> in your own headers, but I think it would be more convenient if rational  
> already provided it out of the box.  
  
I'm not disputing that; but before we put it into boost, we need some tests to make sure it is correct - and even more importantly, make sure it doesn't get broken by changes in boost or the environment.

---

## Comment 5

> Username: mlang  
> Created_at: 2015-04-03 18:04:13 UTC  
> Url: https://github.com/boostorg/rational/pull/6#issuecomment-89377435  

Tests added.

---
