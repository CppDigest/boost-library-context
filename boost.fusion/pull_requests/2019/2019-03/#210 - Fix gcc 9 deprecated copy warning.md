# #210 Fix gcc 9 deprecated copy warning. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2019-03-23 02:00:38 UTC  
> Updated at: 2019-11-18 21:23:10 UTC  
> Closed at: 2019-11-18 21:23:10 UTC  
> Url: https://github.com/boostorg/fusion/pull/210  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-11-18 20:33:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/210#issuecomment-555196885  

Is not #212 supersedes this?

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2019-11-18 20:53:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/210#issuecomment-555203957  

After re-reading both, I would say no, both are actually required.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-11-18 21:13:06 UTC  
> Updated_at: 2019-11-18 21:13:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/210#issuecomment-555211383  

> After re-reading both, I would say no, both are actually required.  
  
I do not understand. Why I am not getting the error here https://wandbox.org/permlink/D3tiubVSsaC9OqdH then?

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2019-11-18 21:23:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/210#issuecomment-555215322  

Indeed you are right. https://en.cppreference.com/w/cpp/language/copy_constructor#Implicitly-declared_copy_constructor reads:  
  
> The generation of the implicitly-defined copy constructor is deprecated if T has a user-defined destructor or user-defined copy assignment operator.  
  
So explicitly deleting the copy assignment does not deprecate the implicitly defined copy constructor.

---
