# #482 - how can build boost with vs2017 and support WinXP [Open]

> Username: mecctor  
> Created at: 2021-02-23 14:49:23 UTC  
> Updated at: 2021-02-23 14:52:16 UTC  
> Url: https://github.com/boostorg/boost/issues/482  

(1) .\b2.exe -j5 --toolset=msvc-14.0-xp --with-date_time --with-filesystem --with-system --with-thread --with-regex link=static runtime-link=static  
  
(2)add  #define BOOST_USE_WINAPI_VERSION BOOST_WINAPI_VERSION_WINXP int file   \boost\winapi\config.hpp  
  
but i work well on win7,win10.but when i put it to Winxp, it crach.

---

## Comment 1

> Username: mclow  
> Created at: 2021-02-23 14:52:16 UTC  
> Url: https://github.com/boostorg/boost/issues/482#issuecomment-784257251  

A better place to ask this question (with more details) is on the boost-users mailing list: boost-users@lists.boost.org  
Many more people will see your question.
