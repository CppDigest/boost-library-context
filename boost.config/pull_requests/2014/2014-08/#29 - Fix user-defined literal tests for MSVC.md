# #29 Fix user-defined literal tests for MSVC [Merged]

> Username: MarcelRaad  
> Created at: 2014-08-12 08:25:17 UTC  
> Updated at: 2014-08-15 20:04:49 UTC  
> Merged at: 2014-08-15 17:10:44 UTC  
> Closed at: 2014-08-15 17:10:44 UTC  
> Url: https://github.com/boostorg/config/pull/29  

The test currently tests not only the compiler's support for user-defined literals, but also its constexpr support, which is only partly implemented in MSVC 14. This change removes the constexpr's that MSVC doesn't like so that the tests pass on MSVC 14 CTP2.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-12 11:03:21 UTC  
> Url: https://github.com/boostorg/config/pull/29#issuecomment-51900199  

I'm not convinced this is the correct fix: given that UDL's are only partly supported in VC14, and that they're next to useless without constexpr anyway, I tend to think the test is valid and UDL support should be disabled for VC14 in Boost.Config.  
  
BTW there is only one library using BOOST_NO_CXX11_USER_DEFINED_LITERALS that I can find: Multiprecision, and that use case definitely requires constexpr as well.  Did you have a use case in mind (preferably within Boost)?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2014-08-12 11:41:28 UTC  
> Url: https://github.com/boostorg/config/pull/29#issuecomment-51903200  

I didn't have any use case in mind, I just noticed that Boost.Build tells me that UDL's are not supported when running the regression test suite even though the macro to disable it is not set (I'm running the tests with BOOST_MSVC_ENABLE_2014_JUN_CTP).  
  
According to http://blogs.msdn.com/b/vcblog/archive/2014/06/03/visual-studio-14-ctp.aspx, the only thing that is "not yet supported" is "extremely large literals", which sounds like an upcoming version will have full UDL support (and which doesn't sound like much of a problem even now).  
  
Without constexpr, UDL's are still shorthand for an explicit constructor call, so I don't think they're completely useless. Of course you're right that enabling them in Boost currently doesn't make much sense, but I think they're orthogonal to constexpr.

---
