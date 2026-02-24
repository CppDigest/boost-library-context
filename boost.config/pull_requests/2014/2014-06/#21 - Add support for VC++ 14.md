# #21 Add support for VC++ 14 [Merged]

> Username: MarcelRaad  
> Created at: 2014-06-05 19:49:04 UTC  
> Updated at: 2014-06-17 10:19:00 UTC  
> Merged at: 2014-06-06 18:22:47 UTC  
> Closed at: 2014-06-06 18:22:47 UTC  
> Url: https://github.com/boostorg/config/pull/21  

All the config tests pass, with the exception of the user-defined literal test, which passes only if "static constexpr" is substituted with "static BOOST_CONSTEXPR_OR_CONST" and "constexpr" is substituted with "BOOST_CONSTEXPR".  
  
See  
http://blogs.msdn.com/b/vcblog/archive/2014/06/03/visual-studio-14-ctp.aspx  
and  
http://blogs.msdn.com/b/somasegar/archive/2014/05/28/first-preview-of-visual-studio-quot-14-quot-available-now.aspx

---

## Comment 1

> Username: Beman  
> Created_at: 2014-06-05 21:10:28 UTC  
> Url: https://github.com/boostorg/config/pull/21#issuecomment-45275323  

In the past we did not automatically support VC++ CTP releases. The last time we offered CTP support, we did it like this:  
  
// C++11 features supported by VC++ 11 (aka 2012) November 2012 CTP  
// Because the CTP is unsupported, unrelease, and only alpha quality,  
// it is only supported if BOOST_MSVC_ENABLE_2012_NOV_CTP is defined.  
//  
#if _MSC_FULL_VER < 170051025 || !defined(BOOST_MSVC_ENABLE_2012_NOV_CTP)  
  
This time around the enabling macro would presumably be BOOST_MSVC_ENABLE_2014_JUN_CTP  
  
I'd be a lot more comfortable if you added the appropriate comment and macro to your pull request.  
  
It saves Boost developers and users future pain if we do some testing on this CTP, so I am in favor of an updated pull request. John Maddock should make final decision, however.  
  
--Beman

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2014-06-06 07:38:29 UTC  
> Url: https://github.com/boostorg/config/pull/21#issuecomment-45310708  

Ah, I thought that was only because the November 2012 CTP was an out-of-band compiler CTP as opposed to the beta of the next major version. I will update the pull request accordingly. Thanks for your response!

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2014-06-06 12:19:58 UTC  
> Url: https://github.com/boostorg/config/pull/21#issuecomment-45330500  

Pull request updated.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2014-06-06 18:22:35 UTC  
> Url: https://github.com/boostorg/config/pull/21#issuecomment-45368802  

It's good to see so many new features in the roadmap.  
  
I would probably argue that user-defined-literals, and noexcept are only partially implemented as yet (just judging by the published roadmap), but it looks like we can and should merge this and see how things go.

---
