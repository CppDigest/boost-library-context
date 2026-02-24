# #28 Add throw/catch test for bad_function_call [Merged]

> Username: pdimov  
> Created at: 2018-10-19 01:07:36 UTC  
> Updated at: 2018-10-26 11:41:26 UTC  
> Merged at: 2018-10-26 11:41:19 UTC  
> Closed at: 2018-10-26 11:41:19 UTC  
> Url: https://github.com/boostorg/function/pull/28  

Curious to see the CI results on this one. Refs #24.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-19 01:51:46 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-431219406  

Works perfectly. :-)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-10-19 02:02:51 UTC  
> Updated_at: 2018-10-19 02:03:14 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-431221070  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/28?src=pr&el=h1) Report  
> Merging [#28](https://codecov.io/gh/boostorg/function/pull/28?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/function/commit/453860ff9cfac59c53bd3936e95324b2e6318b97?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/28/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/28?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #28   +/-   ##  
========================================  
  Coverage    54.24%   54.24%             
========================================  
  Files            4        4             
  Lines          389      389             
  Branches        98       98             
========================================  
  Hits           211      211             
  Misses          85       85             
  Partials        93       93  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/28?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/28?src=pr&el=footer). Last update [453860f...2d6df07](https://codecov.io/gh/boostorg/function/pull/28?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: apolukhin  
> Created_at: 2018-10-19 06:42:40 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-431261200  

That's great news! Chances to break something with setting visibility to hidden seem to be quite low on modern compilers.  
  
I'm interested to see the test results for Intel compiler and for GCC 4.4 or older.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-19 14:39:25 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-431385891  

4.4 is included. Intel... don't exactly make it easy to use their compiler on Travis. :-)

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-10-20 12:25:24 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-431575724  

Shouldn't this test code technically have a header that defines BOOST_SYMBOL_IMPORT on the function being import/exported, for example:  
  
```  
(header included by the library source and the test cpp)  
#include <boost/config.hpp>  
  
#if defined(THROW_BAD_FUNCTION_CALL_DYN_LINK)  
BOOST_SYMBOL_EXPORT  
#else  
BOOST_SYMBOL_IMPORT  
#endif  
void throw_bad_function_call();  
```  
And have both the library source and the consuming test include it?  While this did work on Linux, shouldn't it have failed on Windows without the import?  
  
Also, our documentation at https://www.boost.org/doc/libs/1_68_0/libs/config/doc/html/boost_config/boost_macro_reference.html states:  
  
"Without BOOST_SYMBOL_VISIBLE, it would be impossible to catch my_exception thrown from a shared library compiled by GCC with the -fvisibility=hidden option."  
  
And this test disproves that.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2018-10-20 12:56:54 UTC  
> Updated_at: 2018-10-20 12:57:26 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-431578419  

That's interesting. Could you please also add a test:  
```  
// in shared library  
std::exception& get_ex() { static boost::bad_function_call ex; return ex; }  
  
// in executable  
assert(typeid(get_ex()) == typeid(boost::bad_function_call));  
dynamic_cast<boost::bad_function_call&>(get_ex()); // should not throw  
```

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-10-20 14:33:04 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-431586945  

@jeking3 Functions have always worked without `dllimport`, since before there even was `dllimport`. You do need `dllimport` for variables though.  
  
@apolukhin You can try these yourself - just make another PR from this one. (It's on the branch `pr/shared-throw-test`.)

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-10-25 12:33:59 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-433032737  

@pdimov were you intending this to be merged, or were you just using boostorg CI?

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-10-25 12:52:14 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-433038342  

It wouldn't hurt to test whether `bad_function_call` can be caught, so we might just as well merge it, even if it didn't expose a problem today.

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-10-26 11:41:07 UTC  
> Url: https://github.com/boostorg/function/pull/28#issuecomment-433380053  

Sounds good.  Can't complain about additional testing!  Always a good thing.

---
