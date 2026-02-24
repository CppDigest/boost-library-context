# #4 Vcpkg port [Merged]

> Username: grisumbras  
> Created at: 2023-11-01 10:43:40 UTC  
> Updated at: 2023-11-06 07:25:55 UTC  
> Merged at: 2023-11-05 16:58:52 UTC  
> Closed at: 2023-11-05 16:58:52 UTC  
> Url: https://github.com/boostorg/scope/pull/4  

As promised, a vcpkg port that allows consuming Scope as vcpkg package.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-11-01 11:26:15 UTC  
> Url: https://github.com/boostorg/scope/pull/4#issuecomment-1788798855  

Could you add a license header comment?  
  
Do I understand correctly that the hash sums will require updating whenever new commits are made?

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-11-01 16:00:30 UTC  
> Url: https://github.com/boostorg/scope/pull/4#issuecomment-1789219246  

Also, Boost.Scope requires Boost 1.84 (i.e. the current master or develop).

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-11-01 16:28:07 UTC  
> Url: https://github.com/boostorg/scope/pull/4#issuecomment-1789265857  

> Could you add a license header comment?  
  
Will do.  
  
> Do I understand correctly that the hash sums will require updating whenever new commits are made?  
  
Yes. Normally vcpkg ports aren't parts of the packaged project, but are parts of a separate "repository" project.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-11-01 19:55:21 UTC  
> Url: https://github.com/boostorg/scope/pull/4#issuecomment-1789582155  

> > Do I understand correctly that the hash sums will require updating whenever new commits are made?  
>   
> Yes. Normally vcpkg ports aren't parts of the packaged project, but are parts of a separate "repository" project.  
  
In that case I won't be able to maintain it. It would be ok to keep it for the sake of the review (provided that the 1.84 requirement can be managed), but after that I will probably remove it. If the library is accepted, that shouldn't be a problem, as the library would be packaged as part of Boost.

---

## Comment 5

> Username: grisumbras  
> Created_at: 2023-11-01 22:13:14 UTC  
> Url: https://github.com/boostorg/scope/pull/4#issuecomment-1789762614  

Yes, the intention is for this to be temporary, because vcpkg team makes Boost packages for vcpkg by themselves.

---

## Comment 6

> Username: Lastique  
> Created_at: 2023-11-05 16:59:01 UTC  
> Url: https://github.com/boostorg/scope/pull/4#issuecomment-1793790865  

Thanks.

---
