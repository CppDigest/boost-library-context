# #36 - Possible regressions under g++ 4.4 and clang++ 3.4 when building quickbook [Closed]

> Username: pdimov  
> Created at: 2020-12-30 16:30:24 UTC  
> Updated at: 2021-04-18 11:44:31 UTC  
> Closed at: 2021-04-18 11:44:31 UTC  
> Url: https://github.com/boostorg/move/issues/36  

g++ 4.4:  
```  
gcc.compile.c++ ../../../bin.v2/tools/quickbook/test/quickbook_manual-1_4.test2/gcc-4.4.7/debug/cxxstd-0x-iso/link-static/text_diff.o  
In file included from ../../../boost/move/traits.hpp:31,  
                 from ../../../boost/move/utility.hpp:30,  
                 from ../../../boost/optional/optional.hpp:53,  
                 from ../../../boost/optional.hpp:15,  
                 from ../../../boost/spirit/home/classic/core/match.hpp:15,  
                 from ../../../boost/spirit/home/classic/core/scanner/scanner.hpp:14,  
                 from ../../../boost/spirit/home/classic/core/parser.hpp:14,  
                 from ../../../boost/spirit/home/classic/core/primitives/primitives.hpp:15,  
                 from ../../../boost/spirit/include/classic_primitives.hpp:11,  
                 from /home/travis/boost/tools/quickbook/test/src/text_diff.cpp:15:  
../../../boost/move/detail/type_traits.hpp:306: error: a function call cannot appear in a constant-expression  
../../../boost/move/detail/type_traits.hpp:306: error: an assignment cannot appear in a constant-expression  
../../../boost/move/detail/type_traits.hpp:306: error: a function call cannot appear in a constant-expression  
../../../boost/move/detail/type_traits.hpp:318: error: a function call cannot appear in a constant-expression  
../../../boost/move/detail/type_traits.hpp:318: error: a function call cannot appear in a constant-expression  
```  
https://travis-ci.org/github/boostorg/quickbook/jobs/752114232  
  
clang++ 3.4:  
```  
clang-linux.compile.c++.without-pch ../../../bin.v2/tools/quickbook/test/quickbook_manual-1_4.test2/clang-linux-3.4.2/debug/cxxstd-0x-iso/link-static/text_diff.o  
In file included from /home/travis/boost/tools/quickbook/test/src/text_diff.cpp:15:  
In file included from ../../../boost/spirit/include/classic_primitives.hpp:11:  
In file included from ../../../boost/spirit/home/classic/core/primitives/primitives.hpp:15:  
In file included from ../../../boost/spirit/home/classic/core/parser.hpp:14:  
In file included from ../../../boost/spirit/home/classic/core/scanner/scanner.hpp:14:  
In file included from ../../../boost/spirit/home/classic/core/match.hpp:15:  
In file included from ../../../boost/optional.hpp:15:  
In file included from ../../../boost/optional/optional.hpp:53:  
In file included from ../../../boost/move/utility.hpp:30:  
In file included from ../../../boost/move/traits.hpp:31:  
../../../boost/move/detail/type_traits.hpp:117:10: error: builtin feature check macro requires a parenthesized identifier  
#   elif __has_extension  
         ^  
../../../boost/move/detail/type_traits.hpp:117:25: error: expected value in expression  
#   elif __has_extension  
                        ^  
<scratch space>:181:1: note: expanded from here  
0  
^  
2 errors generated.  
```  
https://travis-ci.org/github/boostorg/quickbook/jobs/752114234  
  
I'm not sure whether we need to care about these old compilers, but the Quickbook travis has them, so it fails.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-12-30 19:28:25 UTC  
> Url: https://github.com/boostorg/move/issues/36#issuecomment-752732429  

Also breaks the Function tests, https://travis-ci.org/github/boostorg/function/builds/752126595  
  
Same comment as above - we could, in principle, declare these as unsupported, if it's too much of a bother. Although the Clang error looks legit.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-04-18 11:44:31 UTC  
> Url: https://github.com/boostorg/move/issues/36#issuecomment-821978628  

Looks like Quickbook tests with Clang 3.4 are now ok:  
  
https://travis-ci.org/github/boostorg/quickbook/jobs/767312070  
  
so I'm closing the issue.
