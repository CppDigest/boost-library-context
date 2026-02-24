# #3 Added Conan recipe [Merged]

> Username: grisumbras  
> Created at: 2023-11-01 09:56:32 UTC  
> Updated at: 2023-11-06 07:26:20 UTC  
> Merged at: 2023-11-05 16:57:22 UTC  
> Closed at: 2023-11-05 16:57:22 UTC  
> Url: https://github.com/boostorg/scope/pull/3  

As promised, here's a simple Conan package recipe that allows consuming Scope as a Conan package

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-11-01 11:23:04 UTC  
> Url: https://github.com/boostorg/scope/pull/3#issuecomment-1788795385  

Could you add a license header comment?

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-11-01 15:59:23 UTC  
> Url: https://github.com/boostorg/scope/pull/3#issuecomment-1789217394  

Also, Boost.Scope requires Boost 1.84 (i.e. the current master or develop).

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-11-01 16:29:47 UTC  
> Url: https://github.com/boostorg/scope/pull/3#issuecomment-1789268482  

> Also, Boost.Scope requires Boost 1.84 (i.e. the current master or develop).  
  
That's going to be a problem: none of the package managers will have Boost 1.84 packages by the time of Scope review.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-11-01 19:59:00 UTC  
> Url: https://github.com/boostorg/scope/pull/3#issuecomment-1789587488  

> > Also, Boost.Scope requires Boost 1.84 (i.e. the current master or develop).  
>   
> That's going to be a problem: none of the package managers will have Boost 1.84 packages by the time of Scope review.  
  
If Conan and vcpkg support patches, you could add a patch that switches from `boost::core::invoke_swap` back to `boost::swap`. This will make the library compatible with 1.83. This is what I had to do for godbolt version, too.

---

## Comment 5

> Username: Lastique  
> Created_at: 2023-11-01 20:04:38 UTC  
> Url: https://github.com/boostorg/scope/pull/3#issuecomment-1789594733  

Here's the patch:  
  
[1.83_compat.patch.gz](https://github.com/Lastique/scope/files/13231313/1.83_compat.patch.gz)

---

## Comment 6

> Username: grisumbras  
> Created_at: 2023-11-02 10:02:52 UTC  
> Url: https://github.com/boostorg/scope/pull/3#issuecomment-1790421759  

They do support patches. I'll update the PRs.

---

## Comment 7

> Username: Lastique  
> Created_at: 2023-11-05 16:57:29 UTC  
> Url: https://github.com/boostorg/scope/pull/3#issuecomment-1793790477  

Thanks.

---
