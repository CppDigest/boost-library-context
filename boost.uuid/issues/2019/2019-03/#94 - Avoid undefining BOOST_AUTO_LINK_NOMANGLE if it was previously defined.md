# #94 - Avoid undefining BOOST_AUTO_LINK_NOMANGLE if it was previously defined. [Closed]

> Username: tomy2105  
> Created at: 2019-03-28 10:07:00 UTC  
> Updated at: 2019-07-24 07:49:29 UTC  
> Closed at: 2019-04-19 10:44:05 UTC  
> Url: https://github.com/boostorg/uuid/issues/94  

Undefining BOOST_AUTO_LINK_NOMANGLE, if it was previously defined, breaks desired behavior of all the boost libraries included after uuid.  
  
Suddenly you will get situation where half of headers link against libname.lib and other half to "fully qualified library name".lib.

---

## Comment 1

> Username: ColinChargyBentley  
> Created at: 2019-07-23 09:41:11 UTC  
> Url: https://github.com/boostorg/uuid/issues/94#issuecomment-514135382  

Hi,  
I've left a comment in https://github.com/boostorg/uuid/pull/95#issuecomment-514135091 about the same kind of bad behavior with BOOST_AUTO_LINK_TAGGED or BOOST_AUTO_LINK_SYSTEM enabled.  
Regards,  
Colin Chargy

---

## Comment 2

> Username: tomy2105  
> Created at: 2019-07-24 07:49:29 UTC  
> Url: https://github.com/boostorg/uuid/issues/94#issuecomment-514521043  

I agree with you completely, as noted in my comment of https://github.com/boostorg/uuid/pull/95, that it would be better not to use boost autolink for non-boost libraries.  
Did not want to go down that path myself, but left that to original developers, because I wasn't sure what was the initial reason to use boost autolinking on non-boost libraries.  
  
However, I'd suggest you open new issue since this one is closed.  
  
In addition please note that, although source of the problem is the same, the problem itself would be different (prior to my fix) when using BOOST_AUTO_LINK_NOMANGLE compared to using BOOST_AUTO_LINK_TAGGED or BOOST_AUTO_LINK_SYSTEM.  
  
If BOOST_AUTO_LINK_NOMANGLE was defined, it would become undefined and all subsequent linking of boost libraries would start behaving differently (as if BOOST_AUTO_LINK_NOMANGLE  was not defined).  
  
On the other hand if BOOST_AUTO_LINK_TAGGED or BOOST_AUTO_LINK_SYSTEM, linking of external libraries in uuid would fail but other boost libs would not be affected.  
  
Hence, due to difference in problem manifestation, BOOST_AUTO_LINK_TAGGED or BOOST_AUTO_LINK_SYSTEM, probably deserve separate issue :).
