# #319 Qi: Permutations parser always initializes optionals [Merged]

> Username: Kojoley  
> Created at: 2017-12-07 15:00:29 UTC  
> Updated at: 2017-12-08 16:46:44 UTC  
> Merged at: 2017-12-08 16:46:40 UTC  
> Closed at: 2017-12-08 16:46:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/319  

Closes https://svn.boost.org/trac10/ticket/12473  
  
Optional parser never fails so `attr = val;` always triggers and initializes  
the `attr` value. The special case for optional attributes could be safely  
removed as any underlying parser must already correctly handle optionals.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-08 00:01:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/319#issuecomment-350132191  

Nods. Good catch!

---
