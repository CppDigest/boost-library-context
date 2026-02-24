# #2 Compiler bug was fixed in VC++ 2013, workaround no longer necessary. [Merged]

> Username: dodheim  
> Created at: 2015-08-24 03:46:45 UTC  
> Updated at: 2019-10-09 17:00:48 UTC  
> Merged at: 2019-08-05 21:57:18 UTC  
> Closed at: 2019-08-05 21:57:18 UTC  
> Url: https://github.com/boostorg/proto/pull/2  

This bug was fixed in VC++ 2013, the workaround is no longer necessary. I noticed the fix as of VC++ 2013 Update 2, so I don't know if the fix was in RTM or in an update, so it may be more prudent to set the value to `1900` instead...

---

## Comment 1

> Username: ericniebler  
> Created_at: 2015-08-26 07:21:12 UTC  
> Url: https://github.com/boostorg/proto/pull/2#issuecomment-134874335  

> so it may be more prudent to set the value to 1900 instead  
  
I think that's wise, unless we can confirm that the fix was in RTM.

---

## Comment 2

> Username: ericniebler  
> Created_at: 2015-09-03 21:38:50 UTC  
> Url: https://github.com/boostorg/proto/pull/2#issuecomment-137581557  

Do you want to change the value to 1900 or should I?

---

## Comment 3

> Username: QuellaZhang  
> Created_at: 2019-08-02 07:01:10 UTC  
> Url: https://github.com/boostorg/proto/pull/2#issuecomment-517581139  

Hi, @dodheim @ericniebler  
  
Thanks for the workaround for MSVC, but the MSVC recently emitted error due to this workaround. And we verified if removed it Boost/proto will build successfully. Can you merge this PR to Boost master branch to remove the workaround? Thank you very much.

---
