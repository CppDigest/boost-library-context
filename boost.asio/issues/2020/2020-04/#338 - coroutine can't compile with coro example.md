# #338 - coroutine can't compile with coro example [Closed]

> Username: patlecat  
> Created at: 2020-04-04 21:33:01 UTC  
> Updated at: 2020-07-03 11:23:47 UTC  
> Closed at: 2020-07-03 11:23:47 UTC  
> Url: https://github.com/boostorg/asio/issues/338  

When taking this example from Beast `http_server_coro.cpp` then I receive the following error, but when I compile "same_fringe.cpp" example from coroutine2 then this compiles fine. (Btw Beast"http_server_async.cpp" compiles and runs fine).  
  
Boost 1.72.0  
MSVC2019  
Windows10  
  
```  
1>------ Build started: Project: Testo01, Configuration: Debug x64 ------  
1>http_server_coro.cpp  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(2364,17): error C2039: 'begin': is not a member of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(38): message : see declaration of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(2369,17): error C2039: 'begin': is not a member of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(38): message : see declaration of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(2374,17): error C2039: 'end': is not a member of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(38): message : see declaration of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(2379,17): error C2039: 'end': is not a member of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(38): message : see declaration of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(2384,17): error C2039: 'begin': is not a member of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(38): message : see declaration of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(2389,17): error C2039: 'end': is not a member of 'boost'  
1>C:\boost\boost_1_72_0\boost\coroutine\asymmetric_coroutine.hpp(38): message : see declaration of 'boost'  
1>Done building project "Testo01.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-06 17:26:01 UTC  
> Url: https://github.com/boostorg/asio/issues/338#issuecomment-609929657  

Fixed in Boost master branch. Will be in the 1.73 release.  
  
This is a regression in Boost.Coroutine rather than Boost.Asio.
