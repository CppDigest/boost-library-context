# #36 Conditionally replace deprecated c++98 features by c++11 ones [Merged]

> Username: DanielaE  
> Created at: 2017-04-22 15:31:39 UTC  
> Updated at: 2017-12-26 16:27:34 UTC  
> Merged at: 2017-08-29 14:41:03 UTC  
> Closed at: 2017-08-29 14:41:03 UTC  
> Url: https://github.com/boostorg/algorithm/pull/36  

change std::random_shuffle -> std::shuffle + random_engine if indicated by the supported language level or Boost configuration macro.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-22 15:34:05 UTC  
> Url: https://github.com/boostorg/algorithm/pull/36#issuecomment-296381176  

These changes are required to pass the tests with msvc 14.0 or 14.1 in C++17 mode (i.e. /std:c++latest)

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2017-04-29 23:52:46 UTC  
> Url: https://github.com/boostorg/algorithm/pull/36#issuecomment-298201783  

@mclow , I think, it's good proposal and we can merge it. Can you merge it, please?

---

## Comment 3

> Username: mclow  
> Created_at: 2017-08-29 14:41:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/36#issuecomment-325685978  

Please watch the test bots and make sure that all the tests are still passing - then remind me in a week to merge to master.

---

## Comment 4

> Username: DanielaE  
> Created_at: 2017-10-08 06:17:36 UTC  
> Url: https://github.com/boostorg/algorithm/pull/36#issuecomment-334986033  

Hello Marshall, I think it's time to merge to master ...

---
