# #417 Fix some mpq rational arithmetic errors: [Merged]

> Username: jzmaddock  
> Created at: 2022-01-22 18:34:29 UTC  
> Updated at: 2022-02-17 12:28:17 UTC  
> Merged at: 2022-01-24 09:07:50 UTC  
> Closed at: 2022-01-24 09:07:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/417  

Multiplication by zero should not proceed via gcd.  
Multiplication by scalar should be restricted to integer types (not floats).  
Add some more tests to catch these cases.

---

## Comment 1

> Username: afabri  
> Created_at: 2022-01-24 13:45:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/417#issuecomment-1020117109  

Thank you very much.

---

## Comment 2

> Username: afabri  
> Created_at: 2022-01-26 07:26:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/417#issuecomment-1021934229  

What is the process and schedule for fixes in the mutiprecision package to make it into the master branch of boost and into a boost release ?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-01-26 09:06:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/417#issuecomment-1021999323  

> What is the process and schedule for fixes in the mutiprecision package to make it into the master branch of boost and into a boost release ?  
  
At some point before the next release we'll merge everything to master, I can't find the release schedule off hand, but Easter sometime I would expect the next Boost release to drop.

---

## Comment 4

> Username: afabri  
> Created_at: 2022-02-17 12:28:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/417#issuecomment-1042899190  

@jzmaddock do you get a kind of notification when this fix makes it to the boost master branch (or is it even you who merges)? If so could you do me a favor and post it in this conversation. Thanks, Andreas

---
