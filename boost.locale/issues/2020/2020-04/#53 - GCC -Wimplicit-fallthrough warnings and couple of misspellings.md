# #53 - GCC -Wimplicit-fallthrough warnings and couple of misspellings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-11 17:39:57 UTC  
> Updated at: 2023-03-04 16:22:51 UTC  
> Closed at: 2023-03-04 16:22:50 UTC  
> Url: https://github.com/boostorg/locale/issues/53  

I built Boost 1.72 with GCC 7.5 with added -Wextra in cxxflags. Problems are still in current code:  
  
./boost/locale/utf.hpp:224:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
./boost/locale/utf.hpp:231:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
libs/locale/src/util/gregorian.cpp:206:27: warning: this statement may fall through [-Wimplicit-fallthrough=]  
libs/locale/src/util/gregorian.cpp:505:21: warning: this statement may fall through [-Wimplicit-fallthrough=]  
libs/locale/src/util/gregorian.cpp:529:21: warning: this statement may fall through [-Wimplicit-fallthrough=]  
libs/locale/src/util/gregorian.cpp:725:28: warning: this statement may fall through [-Wimplicit-fallthrough=]  
  
I may be mistaken, but looks Boost has `BOOST_FALLTHROUGH` for this purpose.  
  
Related: #26  
  
Misspellings:  
  
libs/locale/src/util/gregorian.cpp:206 throght  
libs/locale/src/util/gregorian.cpp:234 indecator

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-03-04 16:22:50 UTC  
> Url: https://github.com/boostorg/locale/issues/53#issuecomment-1454792669  

Fixed by #41
