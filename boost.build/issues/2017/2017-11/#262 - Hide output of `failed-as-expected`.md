# #262 - Hide output of `failed-as-expected` [Closed]

> Username: Kojoley  
> Created at: 2017-11-12 18:11:44 UTC  
> Updated at: 2018-01-26 20:04:59 UTC  
> Closed at: 2018-01-26 19:55:38 UTC  
> Url: https://github.com/boostorg/build/issues/262  

Running `compile-fail` rule produces something like this:  
```  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/classic/test/char_strings_test_fail.test/gcc-gnu-6/debug/cxxstd-11-iso/threadapi-pthread/char_strings_test_fail.o  
In file included from ../../../../boost/optional/optional.hpp:33:0,  
                 from ../../../../boost/optional.hpp:15,  
                 from ../../../../boost/spirit/home/classic/core/match.hpp:15,  
                 from ../../../../boost/spirit/home/classic/core/scanner/scanner.hpp:14,  
                 from ../../../../boost/spirit/home/classic/core/parser.hpp:14,  
                 from ../../../../boost/spirit/home/classic/core/primitives/primitives.hpp:15,  
                 from ../../../../boost/spirit/include/classic_primitives.hpp:11,  
                 from char_strings_test_fail.cpp:10:  
../../../../boost/spirit/home/classic/core/primitives/primitives.hpp: In instantiation of ‘boost::spirit::classic::chlit<CharT> boost::spirit::classic::ch_p(const CharT (&)[N]) [with CharT = char; long unsigned int N = 7ul]’:  
char_strings_test_fail.cpp:18:33:   required from here  
../../../../boost/static_assert.hpp:70:41: error: static assertion failed: N < 3  
 #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
                                         ^  
../../../../boost/spirit/home/classic/core/primitives/primitives.hpp:139:9: note: in expansion of macro ‘BOOST_STATIC_ASSERT’  
         BOOST_STATIC_ASSERT(N < 3);  
         ^~~~~~~~~~~~~~~~~~~  
(failed-as-expected) ../../../../bin.v2/libs/spirit/classic/test/char_strings_test_fail.test/gcc-gnu-6/debug/cxxstd-11-iso/threadapi-pthread/char_strings_test_fail.o  
**passed** ../../../../bin.v2/libs/spirit/classic/test/char_strings_test_fail.test/gcc-gnu-6/debug/cxxstd-11-iso/threadapi-pthread/char_strings_test_fail.test  
```  
  
What should I do to hide the output so I will get:  
  
```  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/classic/test/char_strings_test_fail.test/gcc-gnu-6/debug/cxxstd-11-iso/threadapi-pthread/char_strings_test_fail.o  
(failed-as-expected) ../../../../bin.v2/libs/spirit/classic/test/char_strings_test_fail.test/gcc-gnu-6/debug/cxxstd-11-iso/threadapi-pthread/char_strings_test_fail.o  
**passed** ../../../../bin.v2/libs/spirit/classic/test/char_strings_test_fail.test/gcc-gnu-6/debug/cxxstd-11-iso/threadapi-pthread/char_strings_test_fail.test  
```  
  
I looked docs and googled for it and found nothing. Thanks.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-11-14 20:53:10 UTC  
> Url: https://github.com/boostorg/build/issues/262#issuecomment-344394734  

AMDG  
  
  There is no option to change this behavior.  For the  
purposes of output control, FAIL_EXPECTED targets  
are still considered failed.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-01-26 19:55:38 UTC  
> Url: https://github.com/boostorg/build/issues/262#issuecomment-360888239  

Boost.Build never hides output except for quiet actions.  It would obviously be wrong to make compiling quiet in general.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-01-26 20:04:59 UTC  
> Url: https://github.com/boostorg/build/issues/262#issuecomment-360890604  

This behavior of `compile-fail`/`link-fail` rules really annoys, and not only me, there are several posts on resources like stackoverflow where people asking how to hide `failed-as-expected` output.  
  
> It would obviously be wrong to make compiling quiet in general.  
  
Actually it is obviously wrong to show three monitors output of compile error when test passes without letting people to control their output.
