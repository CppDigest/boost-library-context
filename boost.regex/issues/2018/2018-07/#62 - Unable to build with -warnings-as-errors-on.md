# #62 - Unable to build with <warnings-as-errors>on [Closed]

> Username: jeking3  
> Created at: 2018-07-17 11:35:33 UTC  
> Updated at: 2018-08-25 17:12:15 UTC  
> Closed at: 2018-08-25 17:12:15 UTC  
> Url: https://github.com/boostorg/regex/issues/62  

I am trying to build Boost.Iostreams with warnings-as-errors enabled and came across this warning under clang in c++03 mode:  
```  
../../libs/regex/build/../src/icu.cpp:484:40: error: 'long long' is a C++11 extension [-Werror,-Wc++11-long-long]  
   char_class_type m = char_class_type(1uLL << u_charType(c));  
                                       ^  
1 error generated.  
  
  "clang++" -c -x c++ -std=c++03 -fPIC -m64 -O0 -fno-inline -Wall -pedantic -Werror -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_HAS_ICU=1 -DBOOST_REGEX_DYN_LINK=1 -I"../.." -o "../../bin.v2/libs/regex/build/clang-linux-5.0.0/debug/cxxstd-03-iso/icu.o" "../../libs/regex/build/../src/icu.cpp"  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-17 12:22:24 UTC  
> Url: https://github.com/boostorg/regex/issues/62#issuecomment-405563154  

Is:  
  
```  
char_class_type m = char_class_type(static_cast<char_class_type>(1) << u_charType(c));  
```  
  
Warning free?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-07-17 13:58:42 UTC  
> Url: https://github.com/boostorg/regex/issues/62#issuecomment-405591673  

That does resolve the warning in icu.cpp.  There are more in the unit tests however (unused typedefs for unicode tests)... I will submit a small PR to fix what I could just now including what you suggested.    
  
I cannot get it warning free immediately in C++03 mode because of issues in Boost.Thread:  
```  
In file included from ../../../boost/thread/detail/config.hpp:14:  
../../../boost/thread/detail/thread_safety.hpp:26:38: error: variadic macros are a C99 feature [-Werror,-Wvariadic-macros]  
#define BOOST_THREAD_ACQUIRED_BEFORE(...) \  
                                     ^  
```  
  
And in C++11 I ran into this interesting one:  
```  
clang-linux.compile.c.without-pth ../../../bin.v2/libs/regex/test/posix_api_check.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/c_compiler_checks/posix_api_check.o  
In file included from c_compiler_checks/posix_api_check.c:23:  
In file included from ../../../boost/regex.h:22:  
In file included from ../../../boost/cregex.hpp:27:  
In file included from ../../../boost/regex/v4/cregex.hpp:26:  
In file included from ../../../boost/regex/v4/match_flags.hpp:25:  
In file included from ../../../boost/detail/workaround.hpp:8:  
In file included from ../../../boost/config/workaround.hpp:41:  
In file included from ../../../boost/config.hpp:39:  
../../../boost/config/compiler/clang.hpp:106:11: error: 'BOOST_SYMBOL_EXPORT' macro redefined [-Werror,-Wmacro-redefined]  
#  define BOOST_SYMBOL_EXPORT __attribute__((__visibility__("default")))  
          ^  
../../../boost/regex/config.hpp:226:11: note: previous definition is here  
#  define BOOST_SYMBOL_EXPORT  
          ^  
1 error generated.  
  
  "/usr/bin/clang++-6.0" -c -x c -fPIC -m64 -pthread -O0 -fno-inline -Wall -pedantic -Werror -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_HAS_ICU=1 -DBOOST_REGEX_DYN_LINK=1 -DU_USING_ICU_NAMESPACE=0 -I"../../.." -I"/usr/include" -c -o "../../../bin.v2/libs/regex/test/posix_api_check.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/c_compiler_checks/posix_api_check.o" "c_compiler_checks/posix_api_check.c"  
  
...failed clang-linux.compile.c.without-pth ../../../bin.v2/libs/regex/test/posix_api_check.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/c_compiler_checks/posix_api_check.o...  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-07-18 17:59:37 UTC  
> Url: https://github.com/boostorg/regex/issues/62#issuecomment-406020905  

>../../../boost/config/compiler/clang.hpp:106:11: error: 'BOOST_SYMBOL_EXPORT' macro redefined  
  
I've just pushed a fix for this, also a workaround for a VC15.7 bug which causes:  
  
```  
std::locale l("Unknown_name");  
```  
  
to either hang or debug assert inside the C runtime, which I believe is breaking your PR (I'll restart that shortly).

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-08-25 17:12:15 UTC  
> Url: https://github.com/boostorg/regex/issues/62#issuecomment-415983621  

PR merged, closing down.
