# #1890 - coroutine can't compile with coro example [Closed]

> Username: patlecat  
> Created at: 2020-04-04 22:07:30 UTC  
> Updated at: 2020-04-06 18:18:46 UTC  
> Closed at: 2020-04-06 16:48:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1890  

When taking this example from asio `http_server_coro.cpp` then I receive the following error, but when I compile "same_fringe.cpp" example from coroutine2 then this compiles fine. (Btw "http_server_async.cpp" compiles and runs fine).  
  
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
> Created at: 2020-04-06 16:48:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1890#issuecomment-609909965  

Hi @patlecat,  
  
Thanks for highlighting the issue.  
  
This is actually a regression in Boost.Coroutine in the 1.72 release of Boost.  
  
It has been corrected in the upcoming 1.72 release. I have just tested it against the `master` branch of boost and all is now well.  
  
ps. welcome to slack.

---

## Comment 2

> Username: patlecat  
> Created at: 2020-04-06 18:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1890#issuecomment-609953584  

Which upcoming release exactly? 1.72.1 maybe? And do you know when it will be released?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-06 18:18:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1890#issuecomment-609957080  

1.73.0 and the release schedule is posted on the boost.org website.
