# #72 - Cannot use shared library after switching compiler [Closed]

> Username: jbeich  
> Created at: 2018-11-12 19:11:47 UTC  
> Updated at: 2024-03-25 18:22:11 UTC  
> Closed at: 2024-03-25 18:22:11 UTC  
> Url: https://github.com/boostorg/regex/issues/72  

After https://github.com/boostorg/boost/pull/190 if Regex is built by Clang but later used by GCC the build fails. For one, FreeBSD (Clang by default) sometimes use GCC due to better OpenMP support on non-x86 archs. Affects at least [colmap](https://ptpb.pw/ZYFg). [One workaround](https://reviews.freebsd.org/differential/changeset/?ref=596608) is to assume Boost is built by Clang on libc++ systems.  
  
```c++  
$ cat >test.cc  
#include <boost/regex.hpp>  
  
int main(void) {  
  boost::regex regex(".*");  
  return 0;  
}  
  
$ g++8 -lboost_regex test.cc  
/tmp//ccDvMULY.o: In function `boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::assign(char const*, char const*, unsigned int)':  
test.cc:(.text._ZN5boost11basic_regexIcNS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE6assignEPKcS7_j[_ZN5boost11basic_regexIcNS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE6assignEPKcS7_j]+0x2a): undefined reference to `boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::do_assign(char const*, char const*, unsigned int)'  
collect2: error: ld returned 1 exit status  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-14 16:09:30 UTC  
> Url: https://github.com/boostorg/regex/issues/72#issuecomment-438718433  

Is it fixed by https://github.com/boostorg/regex/commit/40ecdc3f8b52cc6833f05b37610e916824ba88c7?

---

## Comment 2

> Username: jbeich  
> Created at: 2018-11-14 16:47:04 UTC  
> Url: https://github.com/boostorg/regex/issues/72#issuecomment-438732576  

No. ABI isn't compatible i.e., GCC expects external templates that Boost.Regex built by Clang doesn't have.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-11-14 17:38:22 UTC  
> Url: https://github.com/boostorg/regex/issues/72#issuecomment-438751015  

This is something that we have never supported, I'm tempted to close, but I'll leave open in case I find the time to try something.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-03-25 18:22:11 UTC  
> Url: https://github.com/boostorg/regex/issues/72#issuecomment-2018634352  

Regex is now header only, obsolete.
