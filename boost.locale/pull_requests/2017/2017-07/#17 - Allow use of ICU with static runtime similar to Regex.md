# #17 Allow use of ICU with static runtime similar to Regex. [Closed]

> Username: sav-ix  
> Created at: 2017-07-07 15:15:27 UTC  
> Updated at: 2018-08-12 08:43:00 UTC  
> Closed at: 2018-08-12 08:43:00 UTC  
> Url: https://github.com/boostorg/locale/pull/17  

Hello everyone,  
  
Recent ICU versions provide toolchains for builds using MSVC with Static configuration. And Boost.Regex already provide support for builds with Static ICU binaries (see https://github.com/boostorg/regex/commit/acade9f20c9ff439e71b1c919c3c4889bf98aebf).  
But when I tried to build Boost.Locale using MSVC with Static configuration and Static ICU binaries, got error:  
```  
Mixing ICU with a static runtime doesn't work.  
```  
during configuration and build finished without ICU support.  
  
After removing this code from Boost.Locale sources, similar to Boost.Regex (see patch in attachment), Boost was configured for ICU support and size of Boost.Locale binaries increased by the size of ICU Static binaries.  
  
The troubles began during Boost.Locale tests build, since Jamfile contain configuration for Shared builds only (see [**logs**](https://github.com/boostorg/locale/files/1131379/boost_locale_msvc_ICU_test_logs.zip)). Unfortunately I wasn't succeed to port @jzmaddock's patch to Boost.Locale.  
  
Would "ICU with static runtime" builds be added in future Boost.Locale releases? If so, this PR сan be a reminder of the existing issue.  
  
P.S. didn't open a separate issue on Boost bugtracker, since [appropriate ticket](https://svn.boost.org/trac10/ticket/9685) already exists there.  
  
  
Alexander

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2017-07-16 12:44:50 UTC  
> Url: https://github.com/boostorg/locale/pull/17#issuecomment-315606835  

I can't merge it as is if the tests are not working.  
  
Also if you managed to make it work for you, you can always manage the a patch locally. To support static ICU more complete patch required with testing that **all** works in various configurations.  
  
Unless full patch can be provided tested in various configurations, static dynamic and so on I can't merge it, especially since you already can run local build for your own purposes.  
  
In general using static icu and static runtime is very problematic.

---

## Comment 2

> Username: sav-ix  
> Created_at: 2017-07-16 13:16:57 UTC  
> Url: https://github.com/boostorg/locale/pull/17#issuecomment-315608626  

Thank you for answer, Artyom.  
Any chance, that BOOST Developers would add support of static ICU in future Boost.Locale versions? (similar to Boost.Regex https://github.com/boostorg/regex/commit/acade9f20c9ff439e71b1c919c3c4889bf98aebf)

---
