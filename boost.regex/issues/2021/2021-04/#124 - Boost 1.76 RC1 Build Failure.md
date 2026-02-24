# #124 - Boost 1.76 RC1 Build Failure [Closed]

> Username: mborland  
> Created at: 2021-04-10 14:56:18 UTC  
> Updated at: 2021-04-14 19:08:36 UTC  
> Closed at: 2021-04-14 19:08:35 UTC  
> Url: https://github.com/boostorg/regex/issues/124  

Built using GCC-11 on Fedora 34. The following fails:  
  
```  
gcc.compile.c++ bin.v2/libs/regex/build/gcc-11/release/threading-multi/visibility-hidden/wide_posix_api.o  
In file included from ./boost/regex/v5/regex_traits.hpp:26,  
                 from ./boost/regex/regex_traits.hpp:30,  
                 from ./boost/regex/v5/regex.hpp:31,  
                 from ./boost/regex.hpp:34,  
                 from libs/regex/build/../src/wide_posix_api.cpp:25:  
./boost/regex/v5/regex_traits_defaults.hpp: In function ‘bool boost::re_detail_500::is_combining(charT)’:  
./boost/regex/v5/regex_traits_defaults.hpp:594:82: error: ‘numeric_limits’ is not a member of ‘std’  
  594 |    return (c <= static_cast<charT>(0)) ? false : ((c >= static_cast<charT>((std::numeric_limits<uint_least16_t>::max)())) ? false : is_combining_implementation(static_cast<unsigned short>(c)));  
      |                                                                                  ^~~~~~~~~~~~~~  
./boost/regex/v5/regex_traits_defaults.hpp:594:111: error: expected primary-expression before ‘>’ token  
  594 |    return (c <= static_cast<charT>(0)) ? false : ((c >= static_cast<charT>((std::numeric_limits<uint_least16_t>::max)())) ? false : is_combining_implementation(static_cast<unsigned short>(c)));  
      |                                                                                                               ^  
./boost/regex/v5/regex_traits_defaults.hpp:594:114: error: ‘::max’ has not been declared; did you mean ‘std::max’?  
  594 |    return (c <= static_cast<charT>(0)) ? false : ((c >= static_cast<charT>((std::numeric_limits<uint_least16_t>::max)())) ? false : is_combining_implementation(static_cast<unsigned short>(c)));  
      |                                                                                                                  ^~~  
      |                                                                                                                  std::max  
In file included from /usr/include/c++/11/algorithm:62,  
                 from ./boost/regex/v5/regex_workaround.hpp:23,  
                 from ./boost/regex/v5/regex.hpp:29,  
                 from ./boost/regex.hpp:34,  
                 from libs/regex/build/../src/wide_posix_api.cpp:25:  
/usr/include/c++/11/bits/stl_algo.h:3467:5: note: ‘std::max’ declared here  
 3467 |     max(initializer_list<_Tp> __l, _Compare __comp)  
      |     ^~~  
In file included from ./boost/regex/v5/regex_traits.hpp:26,  
                 from ./boost/regex/regex_traits.hpp:30,  
                 from ./boost/regex/v5/regex.hpp:31,  
                 from ./boost/regex.hpp:34,  
                 from libs/regex/build/../src/wide_posix_api.cpp:25:  
./boost/regex/v5/regex_traits_defaults.hpp: In function ‘intmax_t boost::re_detail_500::global_toi(const charT*&, const charT*, int, const traits&)’:  
./boost/regex/v5/regex_traits_defaults.hpp:915:32: error: ‘numeric_limits’ is not a member of ‘std’  
  915 |    std::intmax_t limit = (std::numeric_limits<std::intmax_t>::max)() / radix;  
      |                                ^~~~~~~~~~~~~~  
./boost/regex/v5/regex_traits_defaults.hpp:915:60: error: expected primary-expression before ‘>’ token  
  915 |    std::intmax_t limit = (std::numeric_limits<std::intmax_t>::max)() / radix;  
      |                                                            ^  
./boost/regex/v5/regex_traits_defaults.hpp:915:63: error: ‘::max’ has not been declared; did you mean ‘std::max’?  
  915 |    std::intmax_t limit = (std::numeric_limits<std::intmax_t>::max)() / radix;  
      |                                                               ^~~  
      |                                                               std::max  
In file included from /usr/include/c++/11/algorithm:62,  
                 from ./boost/regex/v5/regex_workaround.hpp:23,  
                 from ./boost/regex/v5/regex.hpp:29,  
                 from ./boost/regex.hpp:34,  
                 from libs/regex/build/../src/wide_posix_api.cpp:25:  
/usr/include/c++/11/bits/stl_algo.h:3467:5: note: ‘std::max’ declared here  
 3467 |     max(initializer_list<_Tp> __l, _Compare __comp)  
      |     ^~~  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_HAS_ICU=1 -DBOOST_REGEX_DYN_LINK=1 -DNDEBUG  -I"." -I"/usr/include"  -c -o "bin.v2/libs/regex/build/gcc-11/release/threading-multi/visibility-hidden/wide_posix_api.o" "libs/regex/build/../src/wide_posix_api.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/regex/build/gcc-11/release/threading-multi/visibility-hidden/wide_posix_api.o...  
```  
  
PR to follow

---

## Comment 1

> Username: mborland  
> Created at: 2021-04-14 19:08:35 UTC  
> Url: https://github.com/boostorg/regex/issues/124#issuecomment-819763720  

Resolved in RC2
