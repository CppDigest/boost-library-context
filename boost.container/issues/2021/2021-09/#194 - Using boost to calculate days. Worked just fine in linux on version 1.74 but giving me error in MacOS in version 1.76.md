# #194 - Using boost to calculate days. Worked just fine in linux on version 1.74 but giving me error in MacOS in version 1.76 [Closed]

> Username: hachi-27  
> Created at: 2021-09-23 19:39:40 UTC  
> Updated at: 2021-09-25 09:45:37 UTC  
> Closed at: 2021-09-24 21:02:34 UTC  
> Url: https://github.com/boostorg/container/issues/194  

### Issue  
I am trying to calculate days based on a particular day of the week using boost 1.76 on macOS with clang 13.0.0. The same code was running just fine on ubuntu with g++ 6.0 using C++14.   
  
### Code  
```   
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/date_time/posix_time/posix_time_io.hpp>  
#include <boost/lexical_cast.hpp>  
```  
PS: Code logic is fine I think. It has something to do with boost the internal codebase.  
  
### Error  
  
![error](https://user-images.githubusercontent.com/60652310/134574188-fd29e5ee-8414-4a24-bacc-9c20b6b603e9.png)  
```  
In file included from /Users/himanshugupta/Desktop/Niveshi/ccapi/example/src/save_to_csv/main.cpp:6:  
In file included from /opt/local/include/boost/date_time/posix_time/posix_time.hpp:24:  
In file included from /opt/local/include/boost/date_time/posix_time/time_formatters.hpp:12:  
In file included from /opt/local/include/boost/date_time/gregorian/gregorian.hpp:31:  
In file included from /opt/local/include/boost/date_time/gregorian/gregorian_io.hpp:16:  
In file included from /opt/local/include/boost/date_time/date_facet.hpp:17:  
In file included from /opt/local/include/boost/algorithm/string/replace.hpp:16:  
In file included from /opt/local/include/boost/range/iterator_range_core.hpp:38:  
In file included from /opt/local/include/boost/range/functions.hpp:20:  
In file included from /opt/local/include/boost/range/size.hpp:25:  
In file included from /opt/local/include/boost/utility.hpp:15:  
/opt/local/include/boost/utility/base_from_member.hpp:146:5: error: unknown type name 'BOOST_PP_REPEAT_1_BOOST_PP_REM'  
    BOOST_PP_REPEAT_FROM_TO( 2, BOOST_PP_INC(BOOST_BASE_FROM_MEMBER_MAX_ARITY),  
    ^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:35:34: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO'  
# define BOOST_PP_REPEAT_FROM_TO BOOST_PP_CAT(BOOST_PP_REPEAT_FROM_TO_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                                 ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
note: (skipping 3 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/repetition/repeat.hpp:38:37: note: expanded from macro 'BOOST_PP_REPEAT_1'  
# define BOOST_PP_REPEAT_1(c, m, d) BOOST_PP_REPEAT_1_I(c, m, d)  
                                    ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/repetition/repeat.hpp:43:39: note: expanded from macro 'BOOST_PP_REPEAT_1_I'  
# define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                      ^  
<scratch space>:17:1: note: expanded from here  
BOOST_PP_REPEAT_1_BOOST_PP_REM  
^   
```  
## Additional info ##  
I think the issue is in these header files.   
Currently, I am building my files using CMake 3.21 with clang 13 with boost 1.76 installed using ```sudo port install boost ``` and I believe code logic is fine as it worked on Linux just fine. Couldn't get anything about this error anywhere so asking here.  
Let me know if I need to provide additional details.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-09-24 21:02:34 UTC  
> Url: https://github.com/boostorg/container/issues/194#issuecomment-926917288  

This is not related to Boost.Container, maybe to Boost.Date Time? (https://github.com/boostorg/date_time)

---

## Comment 2

> Username: hachi-27  
> Created at: 2021-09-25 09:45:37 UTC  
> Url: https://github.com/boostorg/container/issues/194#issuecomment-927096389  

Cool Thanks
