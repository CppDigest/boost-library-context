# #82 - Big endian CI builds fail due to -Wc++11-narrowing [Closed]

> Username: jeking3  
> Created at: 2025-01-02 13:09:48 UTC  
> Updated at: 2025-01-02 13:12:45 UTC  
> Closed at: 2025-01-02 13:12:44 UTC  
> Url: https://github.com/boostorg/mpl/issues/82  

https://github.com/boostorg/mpl/actions/runs/12582526797/job/35068347113#step:12:535  
  
```  
clang-linux.compile.c++ bin.v2/libs/mpl/test/char.test/clang-linux-12/release/s390x_64/cxxstd-17-iso/threading-multi/visibility-hidden/char.o  
libs/mpl/test/char.cpp:23:5: error: non-type template argument evaluates to -1, which cannot be narrowed to type 'char' [-Wc++11-narrowing]  
    BOOST_PP_REPEAT(10, INTEGRAL_WRAPPER_TEST, char)  
    ^  
./boost/preprocessor/repetition/repeat.hpp:30:26: note: expanded from macro 'BOOST_PP_REPEAT'  
# define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                         ^  
./boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
./boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
note: (skipping 11 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
./boost/preprocessor/repetition/limits/repeat_256.hpp:20:36: note: expanded from macro 'BOOST_PP_REPEAT_1_3'  
# define BOOST_PP_REPEAT_1_3(m, d) BOOST_PP_REPEAT_1_2(m, d) m(2, 2, d)  
                                   ^  
./boost/preprocessor/repetition/limits/repeat_256.hpp:19:36: note: expanded from macro 'BOOST_PP_REPEAT_1_2'  
# define BOOST_PP_REPEAT_1_2(m, d) BOOST_PP_REPEAT_1_1(m, d) m(2, 1, d)  
                                   ^  
./boost/preprocessor/repetition/limits/repeat_256.hpp:18:41: note: expanded from macro 'BOOST_PP_REPEAT_1_1'  
# define BOOST_PP_REPEAT_1_1(m, d) m(2, 0, d)  
                                        ^  
1 error generated.  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2025-01-02 13:12:44 UTC  
> Url: https://github.com/boostorg/mpl/issues/82#issuecomment-2567756170  

Duplicate of #50
