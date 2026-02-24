# #15 Fix hidden visibility symbols for mac/clang [Merged]

> Username: memem359  
> Created at: 2017-06-07 21:34:12 UTC  
> Updated at: 2018-11-12 12:01:25 UTC  
> Merged at: 2018-08-22 06:48:57 UTC  
> Closed at: 2018-08-22 06:48:57 UTC  
> Url: https://github.com/boostorg/locale/pull/15  

There is a requirement for BOOST_HAS_DECLSPEC in order to reach the symbol visibility settings. This is only set for Win/gcc builds, and can't be executed for Mac OS/Clang builds. Removing that outside requirement, which was already done for the config files in the other Boost libraries.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-08-20 11:42:10 UTC  
> Url: https://github.com/boostorg/locale/pull/15#issuecomment-414287217  

This fix is essential for https://github.com/boostorg/boost/pull/190.

---

## Comment 2

> Username: jbeich  
> Created_at: 2018-11-12 12:01:25 UTC  
> Url: https://github.com/boostorg/locale/pull/15#issuecomment-437854654  

Can you merge it into `master` branch? FreeBSD and OpenBSD also use Clang by default. For one, [aegisub](https://ptpb.pw/fiiZ), [leatherman](https://ptpb.pw/6sCQ) fail to build due to  
```c++  
$ cat >a.cc  
#include <boost/locale.hpp>  
  
int main(void) {  
  boost::locale::generator gen;  
  return 0;  
}  
  
$ c++ a.cc -lboost_locale  
ld: error: undefined symbol: boost::locale::generator::generator()  
>>> referenced by a.cc  
>>>               /tmp/a-54b9f0.o:(main)  
  
ld: error: undefined symbol: boost::locale::generator::~generator()  
>>> referenced by a.cc  
>>>               /tmp/a-54b9f0.o:(main)  
c++: error: linker command failed with exit code 1 (use -v to see invocation)  
```

---
