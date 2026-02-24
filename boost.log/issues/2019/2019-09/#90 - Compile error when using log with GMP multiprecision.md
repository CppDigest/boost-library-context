# #90 - Compile error when using log with GMP multiprecision [Closed]

> Username: dancooke  
> Created at: 2019-09-09 20:08:34 UTC  
> Updated at: 2019-09-10 14:14:06 UTC  
> Closed at: 2019-09-10 14:14:06 UTC  
> Url: https://github.com/boostorg/log/issues/90  

Using Boost 1.71.0 installed with Homebrew on Mac OS Mojave 10.14.6, the following fails to compile:  
  
```cpp  
#include <boost/log/sinks/text_ostream_backend.hpp>  
#include <boost/multiprecision/gmp.hpp>  
  
int main() {}  
```  
  
```shell  
$ clang++ -v  
Apple LLVM version 10.0.1 (clang-1001.0.46.4)  
Target: x86_64-apple-darwin18.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
  
```shell  
$ clang++ -std=c++14 boost_gmp.cpp -o boost_gmp  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:284:10: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
      if(mpf_sgn(m_data) == 0)  
         ^  
/usr/local/include/gmp.h:2239:26: note: expanded from macro 'mpf_sgn'  
#define mpf_sgn(F) ((F)->_mp_size < 0 ? -1 : (F)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:284:10: error: no member named '_mp_size' in '__mpf_struct'  
      if(mpf_sgn(m_data) == 0)  
         ^~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2239:26: note: expanded from macro 'mpf_sgn'  
#define mpf_sgn(F) ((F)->_mp_size < 0 ? -1 : (F)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:372:84: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
      boost::multiprecision::detail::format_float_string(result, e, org_digits, f, mpf_sgn(m_data) == 0);  
                                                                                   ^  
/usr/local/include/gmp.h:2239:26: note: expanded from macro 'mpf_sgn'  
#define mpf_sgn(F) ((F)->_mp_size < 0 ? -1 : (F)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:372:84: error: no member named '_mp_size' in '__mpf_struct'  
      boost::multiprecision::detail::format_float_string(result, e, org_digits, f, mpf_sgn(m_data) == 0);  
                                                                                   ^~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2239:26: note: expanded from macro 'mpf_sgn'  
#define mpf_sgn(F) ((F)->_mp_size < 0 ? -1 : (F)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1323:27: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
      if((base != 10) && (mpz_sgn(m_data) < 0))  
                          ^  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1323:27: error: no member named '_mp_size' in '__mpz_struct'  
      if((base != 10) && (mpz_sgn(m_data) < 0))  
                          ^~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1364:14: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
      return mpz_cmp_si(m_data, i);  
             ^  
/usr/local/include/gmp.h:2249:6: note: expanded from macro 'mpz_cmp_si'  
   ? mpz_cmp_ui (Z, __GMP_CAST (unsigned long, SI))                     \  
     ^  
/usr/local/include/gmp.h:2246:6: note: expanded from macro 'mpz_cmp_ui'  
   ? mpz_sgn (Z) : _mpz_cmp_ui (Z,UI))  
     ^  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1364:14: error: no member named '_mp_size' in '__mpz_struct'  
      return mpz_cmp_si(m_data, i);  
             ^~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2249:6: note: expanded from macro 'mpz_cmp_si'  
   ? mpz_cmp_ui (Z, __GMP_CAST (unsigned long, SI))                     \  
     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2246:6: note: expanded from macro 'mpz_cmp_ui'  
   ? mpz_sgn (Z) : _mpz_cmp_ui (Z,UI))  
     ^~~~~~~~~~~  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1369:14: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
      return mpz_cmp_ui(m_data, i);  
             ^  
/usr/local/include/gmp.h:2246:6: note: expanded from macro 'mpz_cmp_ui'  
   ? mpz_sgn (Z) : _mpz_cmp_ui (Z,UI))  
     ^  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1369:14: error: no member named '_mp_size' in '__mpz_struct'  
      return mpz_cmp_ui(m_data, i);  
             ^~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2246:6: note: expanded from macro 'mpz_cmp_ui'  
   ? mpz_sgn (Z) : _mpz_cmp_ui (Z,UI))  
     ^~~~~~~~~~~  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1410:11: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
   return mpz_sgn(val.data()) == 0;  
          ^  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1410:11: error: no member named '_mp_size' in '__mpz_struct'  
   return mpz_sgn(val.data()) == 0;  
          ^~~~~~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1653:11: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
   return mpz_sgn(val.data());  
          ^  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1653:11: error: no member named '_mp_size' in '__mpz_struct'  
   return mpz_sgn(val.data());  
          ^~~~~~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1657:8: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
   if (mpz_sgn(val.data()) < 0)  
       ^  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1657:8: error: no member named '_mp_size' in '__mpz_struct'  
   if (mpz_sgn(val.data()) < 0)  
       ^~~~~~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                    ~~~  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1668:17: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
      *result = mpz_sgn(val.data()) < 0 ? (std::numeric_limits<long>::min)()  : (std::numeric_limits<long>::max)();  
                ^  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                         ^  
/usr/local/include/boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
                  ^  
In file included from boost_gmp.cpp:2:  
/usr/local/include/boost/multiprecision/gmp.hpp:1668:17: error: no member named '_mp_size' in '__mpz_struct'  
      *result = mpz_sgn(val.data()) < 0 ? (std::numeric_limits<long>::min)()  : (std::numeric_limits<long>::max)();  
                ^~~~~~~~~~~~~~~~~~~  
/usr/local/include/gmp.h:2238:26: note: expanded from macro 'mpz_sgn'  
#define mpz_sgn(Z) ((Z)->_mp_size < 0 ? -1 : (Z)->_mp_size > 0)  
                    ~~~  ^  
18 errors generated.  
```  
  
* Also fails with Clang 8.0.1.  
* Removing the `#include <boost/log/sinks/text_ostream_backend.hpp>` resolves the issue.  
* Switching the order of the `#include`s resolves the issue.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-09-10 14:14:06 UTC  
> Url: https://github.com/boostorg/log/issues/90#issuecomment-529955252  

I cannot reproduce this on Ubuntu 19.04 with gcc 8.3, but it reproduces with clang 8.0.0. I can clearly see `_mp_size` data member in `__mpz_struct` and `__mpf_struct`, so this must be some compiler bug specific to clang.  
  
My guess is that this bug is triggered by something between Boost.MP11 and Boost.Multiprecision because the following reduced piece of code also fails to compile:  
  
```  
#include <boost/mp11/list.hpp>  
#include <boost/multiprecision/gmp.hpp>  
  
int main() {}  
```  
  
I recommend reporting it to clang [bug tracker](https://bugs.llvm.org/) and either [Boost.MP11](https://github.com/boostorg/mp11/issues/new) or [Boost.Multiprecision](https://github.com/boostorg/multiprecision/issues/new), in case if the library maintainers can suggest a workaround.
