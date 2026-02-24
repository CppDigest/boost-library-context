# #720 - different output when building complete or single lib [Closed]

> Username: TomFD  
> Created at: 2021-04-19 13:03:55 UTC  
> Updated at: 2021-04-23 10:19:32 UTC  
> Closed at: 2021-04-23 10:19:32 UTC  
> Url: https://github.com/boostorg/build/issues/720  

I'm using B2 4.3-git and boost 1.75 source from github.  
  
I'm expecting to get lib with ".. mt-s .." when building boost with:  
```  
b2 --stagedir=./stage/x64 address-model=64 --build-type=complete stage --toolset=msvc-14.2 --threading=multi --runtime-link=static --variant=release --link=static  
```  
However, focusing only on chrono, there is no static multithreaded lib, see dir:  
  
*19.04.2021  14:28            24.940 boost_chrono-vc142-mt-x64-1_75.lib  
19.04.2021  14:28         2.424.608 libboost_chrono-vc142-mt-gd-x64-1_75.lib  
19.04.2021  14:28         2.407.398 libboost_chrono-vc142-mt-sgd-x64-1_75.lib  
19.04.2021  14:28           332.348 libboost_chrono-vc142-mt-x64-1_75.lib*  
  
But when compiling **only** chrono with all other options fixed  
```  
b2 --with-chrono --stagedir=./stage/x64 address-model=64 --build-type=complete stage --toolset=msvc-14.2 --threading=multi --runtime-link=static --variant=release --link=static  
```  
I get the expected **libboost_chrono-vc142-mt-s-x64-1_75.lib**:  
  
*19.04.2021  14:38            25.090 boost_chrono-vc142-mt-gd-x64-1_75.lib  
19.04.2021  14:38            24.940 boost_chrono-vc142-mt-x64-1_75.lib  
19.04.2021  14:38         2.424.608 libboost_chrono-vc142-mt-gd-x64-1_75.lib  
19.04.2021  14:38           329.406 libboost_chrono-vc142-mt-s-x64-1_75.lib  
19.04.2021  14:38         2.407.398 libboost_chrono-vc142-mt-sgd-x64-1_75.lib  
19.04.2021  14:38           332.348 libboost_chrono-vc142-mt-x64-1_75.lib*  
  
  
Why can I not generate it with the first complete run?

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-04-22 21:32:42 UTC  
> Url: https://github.com/boostorg/build/issues/720#issuecomment-825199734  

Try `b2 --stagedir=./stage/x64 address-model=64 --build-type=complete stage toolset=msvc-14.2 threading=multi runtime-link=static variant=release link=static`

---

## Comment 2

> Username: TomFD  
> Created at: 2021-04-23 10:19:32 UTC  
> Url: https://github.com/boostorg/build/issues/720#issuecomment-825558703  

I'm very sorry about this, revisiting my bat file does not show the missing "--" but that seemed to be the reason. Many thanks for the comment! Closing.
