# #289 Parse into associative containers [Merged]

> Username: octopus-prime  
> Created at: 2017-11-23 14:28:57 UTC  
> Updated at: 2017-12-07 10:39:55 UTC  
> Merged at: 2017-11-24 08:35:55 UTC  
> Closed at: 2017-11-24 08:35:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/289  

Bugfix and tests for issue #287 "x3 cannot parse into associative containers anymore"

---

## Comment 1

> Username: octopus-prime  
> Created_at: 2017-11-23 14:31:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/289#issuecomment-346632957  

The tests are useless if not executed...  
We must add container_support.cpp to Jamfile again!

---

## Comment 2

> Username: djowel  
> Created_at: 2017-11-23 22:39:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/289#issuecomment-346705602  

Agreed. I wonder why it's not (or was removed)  in the Jamfile!

---

## Comment 3

> Username: djowel  
> Created_at: 2017-11-23 22:41:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/289#issuecomment-346705739  

Feel free to add it to the Jamfile. Seems you haven't yet in this PR?

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-11-23 23:26:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/289#issuecomment-346708853  

@Kojoley says in #287   
  
> but container_support.cpp does not run because it is not present on Jamfile. I just tested and it passes (not on MSVC, because of the other problem)  
  
So I think `container_support.cpp` is not included because of the problems with MSVC?!

---

## Comment 5

> Username: djowel  
> Created_at: 2017-11-24 03:05:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/289#issuecomment-346732527  

I don't think so. Anyway, If that's the case, then #ifdef it out for now. But, better yet, make it work on MSVC :-)

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-11-24 08:03:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/289#issuecomment-346765667  

So, how to get a feedback from travis and appveyor?

---
