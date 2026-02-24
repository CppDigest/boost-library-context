# #729 Cygwin, FreeBSD and Long Doubles [Merged]

> Username: mborland  
> Created at: 2022-01-03 08:45:39 UTC  
> Updated at: 2022-02-23 12:03:47 UTC  
> Merged at: 2022-02-15 19:16:28 UTC  
> Closed at: 2022-02-15 19:16:28 UTC  
> Url: https://github.com/boostorg/math/pull/729  

Consolidates #727 and #728 to allow for other CI fixes before merge.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-01-03 23:36:26 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1004428968  

@mborland , @jzmaddock : I noticed that we haven't done a standalone for 1.78 yet; would it make sense to use this fix to tag a standalone?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-01-04 08:38:01 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1004617167  

> I noticed that we haven't done a standalone for 1.78 yet; would it make sense to use this fix to tag a standalone?  
  
We could do, there are quite a few cygwin-related CI failures showing though.  Some are trivial limits-set-to-low, others look a bit more worry-some at first glance.

---

## Comment 3

> Username: mborland  
> Created_at: 2022-01-04 08:48:06 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1004623510  

@mkoeppe Does SageMath use the standalone version of Boost.Math? If not I think we are safe to cut the standalone release without this.

---

## Comment 4

> Username: mkoeppe  
> Created_at: 2022-01-04 08:51:34 UTC  
> Updated_at: 2022-01-04 08:51:42 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1004625602  

> @mkoeppe Does SageMath use the standalone version of Boost.Math?  
  
No

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-01-30 10:53:41 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1025117223  

Hi Matt (@mborland) this is not directly related to the exact build issue...  
  
Nonetheless, I've been wondering if the explicit use of the commit filter in the YAML script is still helpful or up-to-date or needed. My understanding is that GHA now comprehends the `[ci skip]` expression in commits. If we agree to establish use of `[ci skip]`, could we remove these commit filter artifacts? Or do you think we need them for another process that I'm not thinking of.

---

## Comment 6

> Username: mborland  
> Created_at: 2022-02-11 13:01:26 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1036191373  

@jzmaddock Can you take a look at this? The error rates that Cygwin has for the special functions are in line with what MinGW produces so I don't see any serious problems there. I think for our purposes we can consider Cygwin as having appropriate long double support.

---

## Comment 7

> Username: mborland  
> Created_at: 2022-02-15 18:32:35 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1040632158  

Ping @jzmaddock

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-02-15 18:46:00 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1040649806  

Looks good to me, just out of interest, what's the issue with the tests that are disabled for cygwin?

---

## Comment 9

> Username: mborland  
> Created_at: 2022-02-15 18:51:41 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1040654713  

> Looks good to me, just out of interest, what's the issue with the tests that are disabled for cygwin?  
  
They all require big object support which still failed when the flag was passed due to a lack of support.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2022-02-15 19:16:20 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1040689822  

>They all require big object support which still failed when the flag was passed due to a lack of support.  
  
Ah, yes, GCC's object size limit on windows is super annoying... merging.  
  
Thanks for this!

---

## Comment 11

> Username: mkoeppe  
> Created_at: 2022-02-15 19:21:18 UTC  
> Url: https://github.com/boostorg/math/pull/729#issuecomment-1040694196  

Thanks a lot for working on this!

---
