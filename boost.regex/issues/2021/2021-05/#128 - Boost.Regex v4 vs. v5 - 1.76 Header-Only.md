# #128 - Boost.Regex v4 vs. v5 / 1.76 Header-Only ... [Closed]

> Username: bilbothebaggins  
> Created at: 2021-05-17 13:35:02 UTC  
> Updated at: 2021-05-18 21:01:59 UTC  
> Closed at: 2021-05-18 21:01:59 UTC  
> Url: https://github.com/boostorg/regex/issues/128  

We are using boost.regex on Windows.  
Prior to 1.76 we used `BOOST_REGEX_DYN_LINK` and the b2 compiled DLL.  
  
It seems with 1.76 boost.regex moved to a header-only release (as per https://www.boost.org/users/history/version_1_76_0.html).  
It also seems that with 1.76 the library is internally now `/v5/` instead of `/v4/` and the v4 version is used in C++03 mode.  
It also seems that the https://www.boost.org/doc/libs/1_76_0/libs/regex/doc/html/boost_regex/install.html is now a bit confusing, even though it does mention the transition.  
Is the https://www.boost.org/doc/libs/1_76_0/libs/regex/doc/html/boost_regex/background/history.html document maintained? It was not changed for 1.76 ?  
  
Is there any documentation or maybe dev list discussion on the changes between the v5 and v4 codebase and it's possible implications?  
  
Me, I've replaced our def of BOOST_REGEX_DYN_LINK with a def of BOOST_REGEX_NO_LIB to make sure we don't link anything anymore. So this is really more of a background check question to see whether I should check anything else.  
  
Thanks.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-05-17 17:51:21 UTC  
> Url: https://github.com/boostorg/regex/issues/128#issuecomment-842516646  

>It also seems that the https://www.boost.org/doc/libs/1_76_0/libs/regex/doc/html/boost_regex/install.html is now a bit confusing, even though it does mention the transition.  
  
Nod.  I think I should move the deprecated C++03 build stuff to a separate section.  
  
>Is the https://www.boost.org/doc/libs/1_76_0/libs/regex/doc/html/boost_regex/background/history.html document maintained? It was not changed for 1.76 ?  
  
Apologies, an oversight on my part, will fix.  
  
>Is there any documentation or maybe dev list discussion on the changes between the v5 and v4 codebase and it's possible implications?  
  
There shouldn't really be any great changes - it now uses std lib components like `shared_ptr` rather than the Boost versions, and on Windows we need to include `<windows.h>`, other than that it's basically the same code but with lots of old compiler workarounds removed.  Folks using new compilers wouldn't have been using those anyway.  
  
HTH.
