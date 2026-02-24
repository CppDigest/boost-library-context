# #60 - Compilation errors with gcc 4.6 [Closed]

> Username: Lastique  
> Created at: 2023-08-22 14:50:22 UTC  
> Updated at: 2023-09-17 12:16:13 UTC  
> Closed at: 2023-09-17 12:16:12 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60  

See [here](https://github.com/boostorg/atomic/actions/runs/5937810113/job/16100971758#step:8:433):  
  
```  
./boost/lexical_cast/bad_lexical_cast.hpp:59:9: error: function 'boost::bad_lexical_cast::~bad_lexical_cast()' defaulted on its first declaration must not have an exception-specification  
./boost/lexical_cast/bad_lexical_cast.hpp:61:9: error: function 'boost::bad_lexical_cast::bad_lexical_cast(const boost::bad_lexical_cast&)' defaulted on its first declaration must not have an exception-specification  
./boost/lexical_cast/bad_lexical_cast.hpp:62:27: error: function 'boost::bad_lexical_cast& boost::bad_lexical_cast::operator=(const boost::bad_lexical_cast&)' defaulted on its first declaration must not have an exception-specification  
```  
  
The compiler does not support `noexcept` specification for defaulted functions.  
  
This is caused by https://github.com/boostorg/lexical_cast/pull/59.

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2023-08-29 23:15:55 UTC  
> Updated at: 2023-08-29 23:16:27 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60#issuecomment-1698263356  

Given that LexicalCast is targetting C++11, doesn't it imply that gcc 4.6 is no longer supported either (I mean, gcc 4.6 has only partial support of C++11) ?  
  
The official Boost 1.82 release notes reads (https://www.boost.org/users/history/version_1_82_0.html):  
  
> This release (1.82) and the next (1.83) will be the last releases offering C++03 support for the above libraries and most of their dependencies.  
>   
> The new baseline requirement for the above libraries, and for most of Boost, will be C++11. Since old compiler versions often implement C++11 only partially, due to missing support or bugs, what compilers are considered to meet the C++11 requirement may vary by library.  
>   
> However, a general rule is that if a compiler does not recognize a C++11 keyword or syntax at all (e.g. use of noexcept, use of constexpr, or defining a =deleted function results in an immediate compile error), or if a compiler does not provide a required C++11 header at all (e.g. trying to include <atomic> or <chrono> fails), or the compiler provides a C++11 feature or a header that is completely unusable, this compiler is not considered a C++11 compiler.  
>   
> This rules out, for instance, Microsoft Visual C++ before 14.0, or GCC before 4.8.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-08-29 23:57:09 UTC  
> Updated at: 2023-08-29 23:59:45 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60#issuecomment-1698295496  

It is up to the library maintainer to decide which compilers to support. Personally, I consider the more supported compilers the better, provided that the support is not too burdening.  
  
As a maintainer of my libraries, which indirectly depend on Boost.LexicalCast, I would prefer to support gcc 4.6, given that's not much of a problem. Hence this report and the associated PR.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-09-02 17:31:33 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60#issuecomment-1703895714  

https://github.com/boostorg/lexical_cast/commit/af5ce2a5fe3321e2849a5ce89f80267c9059995e did not fix this, but made it [worse](https://github.com/boostorg/atomic/actions/runs/6059404518/job/16442489297#step:8:196):  
  
```  
In file included from ./boost/lexical_cast.hpp:31:0,  
                 from libs/thread/src/pthread/thread.cpp:38:  
./boost/lexical_cast/bad_lexical_cast.hpp:48:34: error: expected ';' at end of member declaration  
./boost/lexical_cast/bad_lexical_cast.hpp:48:43: error: 'override' does not name a type  
./boost/lexical_cast/bad_lexical_cast.hpp:53:27: error: expected ';' at end of member declaration  
./boost/lexical_cast/bad_lexical_cast.hpp:53:29: error: 'override' does not name a type  
./boost/lexical_cast/bad_lexical_cast.hpp:53:9: error: looser throw specifier for 'virtual boost::bad_lexical_cast::~bad_lexical_cast()'  
/usr/include/c++/4.6/typeinfo:197:13: error:   overriding 'virtual std::bad_cast::~bad_cast() throw ()'  
In file included from ./boost/date_time/c_time.hpp:20:0,  
                 from ./boost/date_time/time_clock.hpp:16,  
                 from ./boost/thread/thread_time.hpp:9,  
                 from ./boost/thread/lock_types.hpp:18,  
                 from ./boost/thread/pthread/thread_data.hpp:12,  
                 from ./boost/thread/thread_only.hpp:17,  
                 from libs/thread/src/pthread/thread.cpp:11:  
./boost/throw_exception.hpp: In instantiation of 'boost::wrapexcept<boost::bad_lexical_cast>':  
./boost/throw_exception.hpp:165:28:   instantiated from 'void boost::throw_exception(const E&) [with E = boost::bad_lexical_cast]'  
./boost/lexical_cast/bad_lexical_cast.hpp:95:13:   instantiated from 'void boost::conversion::detail::throw_bad_cast() [with S = std::basic_string<char>, T = unsigned int]'  
./boost/lexical_cast.hpp:42:13:   instantiated from 'Target boost::lexical_cast(const Source&) [with Target = unsigned int, Source = std::basic_string<char>]'  
libs/thread/src/pthread/thread.cpp:550:83:   instantiated from here  
./boost/throw_exception.hpp:71:47: error: looser throw specifier for 'virtual boost::wrapexcept<boost::bad_lexical_cast>::~wrapexcept()'  
./boost/exception/exception.hpp:484:13: error:   overriding 'virtual boost::exception_detail::clone_base::~clone_base() noexcept (true)'  
  
    "g++-4.6"   -std=c++0x -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -march=corei7-avx -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX   -I"."  -c -o "bin.v2/libs/thread/build/gcc-4.6/debug/cxxstd-0x-iso/instruction-set-sandy-bridge/threading-multi/visibility-hidden/pthread/thread.o" "libs/thread/src/pthread/thread.cpp"  
```

---

## Comment 4

> Username: apolukhin  
> Created at: 2023-09-07 08:35:27 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60#issuecomment-1709711687  

Did the https://github.com/boostorg/lexical_cast/commit/515e5988b2b38132549e5a6f18b64f1ece4cca14 fix the issue?

---

## Comment 5

> Username: Lastique  
> Created at: 2023-09-07 08:48:01 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60#issuecomment-1709737724  

I didn't test, but I'm sure it won't fix it as the compiler doesn't support `override`. See the error message I posted above.

---

## Comment 6

> Username: apolukhin  
> Created at: 2023-09-17 10:03:38 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60#issuecomment-1722441638  

Should be fixed by now

---

## Comment 7

> Username: Lastique  
> Created at: 2023-09-17 12:16:13 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/60#issuecomment-1722464171  

Yes, the CI has passed. Thank you.
