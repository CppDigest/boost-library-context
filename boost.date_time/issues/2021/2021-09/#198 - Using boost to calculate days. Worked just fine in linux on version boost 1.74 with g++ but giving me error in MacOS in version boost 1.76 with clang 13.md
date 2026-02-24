# #198 - Using boost to calculate days. Worked just fine in linux on version boost 1.74 with g++ but giving me error in MacOS in version boost 1.76 with clang 13 [Closed]

> Username: hachi-27  
> Created at: 2021-09-25 10:12:07 UTC  
> Updated at: 2021-09-26 19:26:40 UTC  
> Closed at: 2021-09-26 19:26:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/198  

## Issue ##  
  
I am trying to calculate days based on a particular day of the week using boost 1.76 on macOS with clang 13.0.0. The same code was running just fine on ubuntu with g++ 6.0 using C++14.  
  
## Code Lines ##  
    The error lies in header files which is why adding only the header files I am using for the purpose.  
  
    #include <boost/date_time/posix_time/posix_time.hpp>  
    #include <boost/date_time/posix_time/posix_time_io.hpp>  
    #include <boost/lexical_cast.hpp>  
----------  
## Error ##  
  
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
/opt/local/include/boost/utility/base_from_member.hpp:146:5: error: unknown type name 'BOOST_PP_SUB_P'  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:35:34: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO'  
# define BOOST_PP_REPEAT_FROM_TO BOOST_PP_CAT(BOOST_PP_REPEAT_FROM_TO_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                                 ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
note: (skipping 1 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:49:49: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_1'  
# define BOOST_PP_REPEAT_FROM_TO_1(f, l, m, dt) BOOST_PP_REPEAT_FROM_TO_D_1(BOOST_PP_DEC(BOOST_PP_AUTO_REC(BOOST_PP_WHILE_P, 256)), f, l, m, dt)  
                                                ^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:81:75: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_D_1'  
#    define BOOST_PP_REPEAT_FROM_TO_D_1(d, f, l, m, dt) BOOST_PP_REPEAT_1(BOOST_PP_SUB_D(d, l, f), BOOST_PP_REPEAT_FROM_TO_M_1, (d, f, m, dt))  
                                                                          ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/sub.hpp:44:84: note: expanded from macro 'BOOST_PP_SUB_D'  
#    define BOOST_PP_SUB_D(d, x, y) BOOST_PP_TUPLE_ELEM(2, 0, BOOST_PP_WHILE_ ## d(BOOST_PP_SUB_P, BOOST_PP_SUB_O, (x, y)))  
                                                                                   ^  
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
/opt/local/include/boost/utility/base_from_member.hpp:146:5: error: unknown type name 'BOOST_PP_SUB_O'  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:35:34: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO'  
# define BOOST_PP_REPEAT_FROM_TO BOOST_PP_CAT(BOOST_PP_REPEAT_FROM_TO_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                                 ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
note: (skipping 1 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:49:49: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_1'  
# define BOOST_PP_REPEAT_FROM_TO_1(f, l, m, dt) BOOST_PP_REPEAT_FROM_TO_D_1(BOOST_PP_DEC(BOOST_PP_AUTO_REC(BOOST_PP_WHILE_P, 256)), f, l, m, dt)  
                                                ^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:81:75: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_D_1'  
#    define BOOST_PP_REPEAT_FROM_TO_D_1(d, f, l, m, dt) BOOST_PP_REPEAT_1(BOOST_PP_SUB_D(d, l, f), BOOST_PP_REPEAT_FROM_TO_M_1, (d, f, m, dt))  
                                                                          ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/sub.hpp:44:100: note: expanded from macro 'BOOST_PP_SUB_D'  
#    define BOOST_PP_SUB_D(d, x, y) BOOST_PP_TUPLE_ELEM(2, 0, BOOST_PP_WHILE_ ## d(BOOST_PP_SUB_P, BOOST_PP_SUB_O, (x, y)))  
                                                                                                   ^  
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
/opt/local/include/boost/utility/base_from_member.hpp:146:33: error: expected ')'  
    BOOST_PP_REPEAT_FROM_TO( 2, BOOST_PP_INC(BOOST_BASE_FROM_MEMBER_MAX_ARITY),  
                                ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/inc.hpp:22:29: note: expanded from macro 'BOOST_PP_INC'  
#    define BOOST_PP_INC(x) BOOST_PP_INC_I(x)  
                            ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/inc.hpp:28:28: note: expanded from macro 'BOOST_PP_INC_I'  
# define BOOST_PP_INC_I(x) BOOST_PP_INC_ ## x  
                           ^  
<scratch space>:120:1: note: expanded from here  
BOOST_PP_INC_10  
^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/inc.hpp:40:26: note: expanded from macro 'BOOST_PP_INC_10'  
# define BOOST_PP_INC_10 11  
                         ^  
/opt/local/include/boost/utility/base_from_member.hpp:146:5: note: to match this '('  
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
note: (skipping 1 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:49:49: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_1'  
# define BOOST_PP_REPEAT_FROM_TO_1(f, l, m, dt) BOOST_PP_REPEAT_FROM_TO_D_1(BOOST_PP_DEC(BOOST_PP_AUTO_REC(BOOST_PP_WHILE_P, 256)), f, l, m, dt)  
                                                ^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:81:75: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_D_1'  
#    define BOOST_PP_REPEAT_FROM_TO_D_1(d, f, l, m, dt) BOOST_PP_REPEAT_1(BOOST_PP_SUB_D(d, l, f), BOOST_PP_REPEAT_FROM_TO_M_1, (d, f, m, dt))  
                                                                          ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/sub.hpp:44:116: note: expanded from macro 'BOOST_PP_SUB_D'  
#    define BOOST_PP_SUB_D(d, x, y) BOOST_PP_TUPLE_ELEM(2, 0, BOOST_PP_WHILE_ ## d(BOOST_PP_SUB_P, BOOST_PP_SUB_O, (x, y)))  
                                                                                                                   ^  
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
/opt/local/include/boost/utility/base_from_member.hpp:146:33: error: C++ requires a type specifier for all declarations  
    BOOST_PP_REPEAT_FROM_TO( 2, BOOST_PP_INC(BOOST_BASE_FROM_MEMBER_MAX_ARITY),  
                                ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/inc.hpp:22:29: note: expanded from macro 'BOOST_PP_INC'  
#    define BOOST_PP_INC(x) BOOST_PP_INC_I(x)  
                            ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/inc.hpp:28:28: note: expanded from macro 'BOOST_PP_INC_I'  
# define BOOST_PP_INC_I(x) BOOST_PP_INC_ ## x  
                           ^  
<scratch space>:120:1: note: expanded from here  
BOOST_PP_INC_10  
^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/inc.hpp:40:26: note: expanded from macro 'BOOST_PP_INC_10'  
# define BOOST_PP_INC_10 11  
                         ^  
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
/opt/local/include/boost/utility/base_from_member.hpp:146:5: error: unknown type name 'BOOST_PP_REPEAT_FROM_TO_M_1'  
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
<scratch space>:119:1: note: expanded from here  
BOOST_PP_REPEAT_FROM_TO_1  
^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:49:49: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_1'  
# define BOOST_PP_REPEAT_FROM_TO_1(f, l, m, dt) BOOST_PP_REPEAT_FROM_TO_D_1(BOOST_PP_DEC(BOOST_PP_AUTO_REC(BOOST_PP_WHILE_P, 256)), f, l, m, dt)  
                                                ^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:81:100: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_D_1'  
#    define BOOST_PP_REPEAT_FROM_TO_D_1(d, f, l, m, dt) BOOST_PP_REPEAT_1(BOOST_PP_SUB_D(d, l, f), BOOST_PP_REPEAT_FROM_TO_M_1, (d, f, m, dt))  
                                                                                                   ^  
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
/opt/local/include/boost/utility/base_from_member.hpp:146:5: error: expected ')'  
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
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/dec.hpp:28:28: note: expanded from macro 'BOOST_PP_DEC_I'  
# define BOOST_PP_DEC_I(x) BOOST_PP_DEC_ ## x  
                           ^  
<scratch space>:13:1: note: expanded from here  
BOOST_PP_DEC_1  
^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/dec.hpp:31:25: note: expanded from macro 'BOOST_PP_DEC_1'  
# define BOOST_PP_DEC_1 0  
                        ^  
/opt/local/include/boost/utility/base_from_member.hpp:146:5: note: to match this '('  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:35:34: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO'  
# define BOOST_PP_REPEAT_FROM_TO BOOST_PP_CAT(BOOST_PP_REPEAT_FROM_TO_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                                 ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
<scratch space>:119:1: note: expanded from here  
BOOST_PP_REPEAT_FROM_TO_1  
^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:49:49: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_1'  
# define BOOST_PP_REPEAT_FROM_TO_1(f, l, m, dt) BOOST_PP_REPEAT_FROM_TO_D_1(BOOST_PP_DEC(BOOST_PP_AUTO_REC(BOOST_PP_WHILE_P, 256)), f, l, m, dt)  
                                                ^  
/opt/local/include/boost/preprocessor/repetition/repeat_from_to.hpp:81:129: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO_D_1'  
#    define BOOST_PP_REPEAT_FROM_TO_D_1(d, f, l, m, dt) BOOST_PP_REPEAT_1(BOOST_PP_SUB_D(d, l, f), BOOST_PP_REPEAT_FROM_TO_M_1, (d, f, m, dt))  
                                                                                                                                ^  
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
/opt/local/include/boost/utility/base_from_member.hpp:146:5: error: C++ requires a type specifier for all declarations  
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
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/dec.hpp:28:28: note: expanded from macro 'BOOST_PP_DEC_I'  
# define BOOST_PP_DEC_I(x) BOOST_PP_DEC_ ## x  
                           ^  
<scratch space>:13:1: note: expanded from here  
BOOST_PP_DEC_1  
^  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/arithmetic/dec.hpp:31:25: note: expanded from macro 'BOOST_PP_DEC_1'  
# define BOOST_PP_DEC_1 0  
                        ^  
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
/opt/local/include/boost/utility/base_from_member.hpp:147:32: error: expected ';' at end of declaration list  
     BOOST_PRIVATE_CTR_DEF, _ )  
                               ^  
  
 ```  
  
## Additional info ##  
I think the issue is in these header files.   
Currently, I am building my files using CMake 3.21 with clang 13 with boost 1.76 installed using ```sudo port install boost ``` and I believe code logic is fine as it worked on Linux just fine. Couldn't get anything about this error anywhere so asking here.  
Let me know if I need to provide additional details.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-09-25 17:08:20 UTC  
> Updated at: 2021-09-25 17:08:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/198#issuecomment-927152778  

So I'm unable to replicate the issue with the closest environment I can muster  
  
https://godbolt.org/z/eqf8jMr69  
  
The upstream headers are for sure the issue.  Looking carefully at the output I think you might have 2 versions of boost getting mixed together somehow.  
  
Note this header is in your /Users  
/Users/himanshugupta/Desktop/Niveshi/ccapi/dependency/boost/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
  
And this one is in /opt  
In file included from /opt/local/include/boost/date_time/posix_time/posix_time.hpp:24:  
  
Believe there's a verbose flag in cmake which will dump include paths and such when the compile runs -- you'll probably need that to debug.

---

## Comment 2

> Username: hachi-27  
> Created at: 2021-09-26 19:16:26 UTC  
> Url: https://github.com/boostorg/date_time/issues/198#issuecomment-927356058  

Thank you so much. It worked.  
Yes, it was due to multiple versions of boost.
