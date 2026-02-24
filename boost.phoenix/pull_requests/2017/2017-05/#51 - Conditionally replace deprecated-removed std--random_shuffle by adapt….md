# #51 Conditionally replace deprecated/removed std::random_shuffle by adapt… [Closed]

> Username: DanielaE  
> Created at: 2017-05-13 08:16:31 UTC  
> Updated at: 2017-12-26 16:47:54 UTC  
> Closed at: 2017-12-26 16:47:54 UTC  
> Url: https://github.com/boostorg/phoenix/pull/51  

…ing std::shuffle.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: Flast  
> Created_at: 2017-05-15 01:27:06 UTC  
> Url: https://github.com/boostorg/phoenix/pull/51#issuecomment-301355363  

`phoenix::random_shuffle` should be just a lazy version of std's, i.e. implement and providing `phoenix::shuffle` is better.

---

## Comment 2

> Username: Flast  
> Created_at: 2017-05-15 01:31:22 UTC  
> Url: https://github.com/boostorg/phoenix/pull/51#issuecomment-301355809  

Also, I'm planning to provide newcomer (not only shuffle) algorithms to next release, ;)

---

## Comment 3

> Username: djowel  
> Created_at: 2017-05-15 01:32:07 UTC  
> Url: https://github.com/boostorg/phoenix/pull/51#issuecomment-301355884  

wonderful!

---

## Comment 4

> Username: DanielaE  
> Created_at: 2017-05-15 05:10:41 UTC  
> Url: https://github.com/boostorg/phoenix/pull/51#issuecomment-301377647  

Great!  
  
Pleace bear in mind, users of msvc 14.x (i.e. VS2015 and VS2017) in compilation modes "later than C++14" need my proposed patch at least as an interim solution because std::random_shuffle is gone without a trace.

---
