# #9 Fix chrono to support GCC 3.x [Merged]

> Username: igaztanaga  
> Created at: 2015-10-14 20:01:16 UTC  
> Updated at: 2015-10-18 12:50:56 UTC  
> Merged at: 2015-10-18 12:50:56 UTC  
> Closed at: 2015-10-18 12:50:56 UTC  
> Url: https://github.com/boostorg/chrono/pull/9  

According to the latest compiler deprecation GCC 3.3 is still (in theory) supported. I'm trying to put a regression runner with mingw-3.4 and it seems that "-Wno-variadic-macros" was introduced with GCC 4.0 and compilation fails because of this. With this patch the library compiles fine.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-10-14 20:35:48 UTC  
> Url: https://github.com/boostorg/chrono/pull/9#discussion_r42049218  

You probably meant to remove gcc instead of clang?

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2015-10-18 11:32:01 UTC  
> Url: https://github.com/boostorg/chrono/pull/9#discussion_r42320016  

Yes, sorry, Copy & paste mistake.

---
