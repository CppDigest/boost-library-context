# #27 - Resolve cyclic dependency between lexical_cast and math [Closed]

> Username: jeking3  
> Created at: 2019-05-05 11:34:11 UTC  
> Updated at: 2020-12-15 15:20:37 UTC  
> Closed at: 2020-12-15 15:20:36 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27  

math depends on lexical_cast in a number of places, however lexical_cast depends on math:  
  
```  
include/boost/lexical_cast/detail/inf_nan.hpp:#include <boost/math/special_functions/sign.hpp>  
include/boost/lexical_cast/detail/inf_nan.hpp:#include <boost/math/special_functions/fpclassify.hpp>  
```  
  
Direct dependency cycles should be avoided to prepare boost for a future where each repository releases independently.

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-05-05 16:03:46 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-489439528  

I'd appreciate ideas on how to break that dependency circle.

---

## Comment 2

> Username: jeking3  
> Created at: 2019-05-05 16:09:40 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-489440042  

Perhaps a good idea to work with the math folks on that.  I wanted to identify the direct dependency cycles that are left and report them.  I actually slapped something together this morning to find and visualize these cycles: https://github.com/jeking3/boost-deptree

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-05-05 18:06:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-489449287  

There was avery good idea to move those math functions into a separate library. They are used pretty widely in boost, so moving them into a separate library or core/util will drop a lot of dependencies on math. Can not find the discussion on mailing list :(

---

## Comment 4

> Username: jeking3  
> Created at: 2019-05-06 13:03:45 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-489612475  

Sounds like a good idea if it will clean up this cyclic repo dep and reduce the overall dep tree.  Perhaps @pdimov remembers this conversation?

---

## Comment 5

> Username: pdimov  
> Created at: 2019-05-06 13:17:50 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-489616777  

https://github.com/boostorg/math/issues/151 I suppose.

---

## Comment 6

> Username: JeffGarland  
> Created at: 2020-03-16 03:07:50 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-599323875  

and as a related issue since math seems to be dropping support for 98, lexical_cast now causes all sorts of warnings -- I can file as a separate issue if folks prefer, but on solution is to move the needed functions to lexical_cast and remove the math dependence.  I plan to remove lexical_cast from date_time for newer compilers, but for 98 mode it's needed.  
  
clang-darwin.compile.c++ ../../bin.v2/libs/date_time/example/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/gregorian/dates_as_strings.o  
  
In file included from ../../libs/date_time/example/gregorian/dates_as_strings.cpp:12:  
  
In file included from ../../boost/date_time/gregorian/gregorian.hpp:31:  
  
In file included from ../../boost/date_time/gregorian/gregorian_io.hpp:16:  
  
In file included from ../../boost/date_time/date_facet.hpp:23:  
  
In file included from ../../boost/date_time/period_parser.hpp:14:  
  
In file included from ../../boost/date_time/string_parse_tree.hpp:13:  
  
In file included from ../../boost/lexical_cast.hpp:32:  
  
In file included from ../../boost/lexical_cast/try_lexical_convert.hpp:44:  
  
In file included from ../../boost/lexical_cast/detail/converter_lexical.hpp:54:  
  
In file included from ../../boost/lexical_cast/detail/converter_lexical_streams.hpp:63:  
  
In file included from ../../boost/lexical_cast/detail/inf_nan.hpp:34:  
  
In file included from ../../boost/math/special_functions/sign.hpp:16:  
  
../../boost/math/tools/config.hpp:42:9: warning: CAUTION: One or more C++11 features were found to be unavailable [-W#pragma-messages]  
  
#pragma message("CAUTION: One or more C++11 features were found to be unavailable")  
  
        ^  
  
../../boost/math/tools/config.hpp:43:9: warning: CAUTION: Compiling Boost.Math in pre-C++11 conformance modes is now deprecated and will be removed from March 2021. [-W#pragma-messages]  
  
#pragma message("CAUTION: Compiling Boost.Math in pre-C++11 conformance modes is now deprecated and will be removed from March 2021.")  
  
        ^  
  
../../boost/math/tools/config.hpp:44:9: warning: CAUTION: Define BOOST_MATH_DISABLE_DEPRECATED_03_WARNING to suppress this message. [-W#pragma-messages]  
  
#pragma message("CAUTION: Define BOOST_MATH_DISABLE_DEPRECATED_03_WARNING to suppress this message.")

---

## Comment 7

> Username: pdimov  
> Created at: 2020-11-23 16:59:28 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-732291488  

https://github.com/boostorg/lexical_cast/pull/37

---

## Comment 8

> Username: JeffGarland  
> Created at: 2020-11-25 01:32:07 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-733405565  

Thanks Peter -- much appreciated.

---

## Comment 9

> Username: apolukhin  
> Created at: 2020-12-15 15:20:36 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/27#issuecomment-745360794  

Fixed in https://github.com/boostorg/lexical_cast/pull/37
