# #24 - Cannot link shared library in C++14 or later [Closed]

> Username: jbeich  
> Created at: 2018-07-10 17:12:22 UTC  
> Updated at: 2018-07-10 20:52:20 UTC  
> Closed at: 2018-07-10 17:23:59 UTC  
> Url: https://github.com/boostorg/system/issues/24  

After 7b6dcf6ac6 the following doesn't work, breaking a number of consumers. Adding `-DBOOST_NO_CXX14_CONSTEXPR` appears to help. Is this intentional?  
  
```c++  
$ cat >a.cc  
#include <boost/system/error_code.hpp>  
  
int main()  
{  
  return 0;  
}  
```  
```  
$ pkg info -x boost  
boost-libs-1.68.0.beta1.rc2  
  
$ c++ -v  
FreeBSD clang version 6.0.1 (tags/RELEASE_601/final 335540) (based on LLVM 6.0.1)  
Target: x86_64-unknown-freebsd12.0  
Thread model: posix  
InstalledDir: /usr/bin  
  
$ c++ a.cc -std=c++14 -I/usr/local/include -L/usr/local/lib -lboost_system  
/usr/bin/ld: error: undefined symbol: boost::system::detail::generic_category_instance  
>>> referenced by a.cc  
>>>               /tmp/a-912c13.o:(boost::system::generic_category())  
c++: error: linker command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 1

> Username: jbeich  
> Created at: 2018-07-10 17:23:59 UTC  
> Url: https://github.com/boostorg/system/issues/24#issuecomment-403901985  

Ah, Boost.System itself has to be built with `-std=c++14`. I was building with `-std=gnu++11`, so Boost.Config auto-enabled `cxx11_constexpr`.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-07-10 20:52:20 UTC  
> Url: https://github.com/boostorg/system/issues/24#issuecomment-403961848  

Yes. I think I support the reverse case, linking from C++11 to a C++14-built System, but not this one. (And linking from 03 to 11 should work in both directions.) But in general this kind of thing (mixing C++ dialects) is unlikely to work.
