# #51 Conditionally replace deprecated/removed C++98 function adapters and … [Merged]

> Username: DanielaE  
> Created at: 2017-05-05 12:19:30 UTC  
> Updated at: 2017-12-23 10:24:23 UTC  
> Merged at: 2017-06-30 11:43:00 UTC  
> Closed at: 2017-06-30 11:43:00 UTC  
> Url: https://github.com/boostorg/range/pull/51  

…std::random_shuffle by equivalents.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-05-05 12:21:41 UTC  
> Url: https://github.com/boostorg/range/pull/51#issuecomment-299450543  

Compiles and passes range's testsuite with msvc 9.0, 10.0, 12.0, and 14.1 - the latter in C++17 mode which defines BOOST_NO_CXX98_RANDOM_SHUFFLE and triggers the new code paths.

---
