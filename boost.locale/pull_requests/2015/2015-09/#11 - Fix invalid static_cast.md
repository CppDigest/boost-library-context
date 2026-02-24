# #11 Fix invalid static_cast. [Closed]

> Username: BenWiederhake  
> Created at: 2015-09-20 09:25:56 UTC  
> Updated at: 2015-09-24 19:21:27 UTC  
> Closed at: 2015-09-20 18:58:31 UTC  
> Url: https://github.com/boostorg/locale/pull/11  

This fixes the issue described in https://svn.boost.org/trac/boost/ticket/11673 ("Invalid static_cast"). All regression tests pass. In case you want a PR against master, see my branch "master".

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-09-20 11:47:53 UTC  
> Url: https://github.com/boostorg/locale/pull/11#issuecomment-141781200  

This change is also included in #9.

---

## Comment 2

> Username: BenWiederhake  
> Created_at: 2015-09-20 18:58:29 UTC  
> Url: https://github.com/boostorg/locale/pull/11#issuecomment-141821154  

Indeed! I'm closing this in favor of the better explained and executed #9. (This PR is kind of hacked together.)

---

## Comment 3

> Username: artyom-beilis  
> Created_at: 2015-09-24 09:59:07 UTC  
> Url: https://github.com/boostorg/locale/pull/11#issuecomment-142878601  

No #9 isn't good fix it also removes "map" instread of unordered map support (there is a good reason to keep it - no details there)  
  
#11 is actually better one. It can be pushed

---

## Comment 4

> Username: artyom-beilis  
> Created_at: 2015-09-24 19:21:27 UTC  
> Url: https://github.com/boostorg/locale/pull/11#issuecomment-143024441  

Ok... the problem is that the code that used unordered_map had never actually was used due to a simple fact that version.hpp wasn't included and it never got to be compiled.  
  
I consider removing entire unordered part as it was never properly tested

---
