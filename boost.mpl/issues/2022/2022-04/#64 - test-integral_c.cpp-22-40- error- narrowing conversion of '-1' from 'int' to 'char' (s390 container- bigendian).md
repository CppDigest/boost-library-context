# #64 - test/integral_c.cpp:22:40: error: narrowing conversion of '-1' from 'int' to 'char' (s390 container: bigendian) [Closed]

> Username: jeking3  
> Created at: 2022-04-30 21:10:00 UTC  
> Updated at: 2022-04-30 21:18:28 UTC  
> Closed at: 2022-04-30 21:18:28 UTC  
> Url: https://github.com/boostorg/mpl/issues/64  

```  
gcc.compile.c++ ../../bin.v2/libs/mpl/test/integral_c.test/gcc-11/debug/threading-multi/visibility-hidden/integral_c.o  
In file included from ../../boost/mpl/aux_/test/assert.hpp:17,  
                 from ../../boost/mpl/aux_/test.hpp:19,  
                 from test/integral_wrapper_test.hpp:15,  
                 from test/integral_c.cpp:17:  
test/integral_c.cpp: In function 'void test20()':  
test/integral_c.cpp:22:40: error: narrowing conversion of '-1' from 'int' to 'char' [-Wnarrowing]  
   22 | #   define WRAPPER(T, i) integral_c<T,i>  
      |                                        ^  
../../boost/mpl/assert.hpp:60:58: note: in definition of macro 'BOOST_MPL_AUX_ASSERT_CONSTANT'  
   60 | #   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
      |                                                          ^~~~  
../../boost/mpl/aux_/test/assert.hpp:20:41: note: in expansion of macro 'BOOST_MPL_ASSERT'  
   20 | #define MPL_ASSERT(pred)                BOOST_MPL_ASSERT(pred)  
      |                                         ^~~~~~~~~~~~~~~~  
test/integral_wrapper_test.hpp:57:7: note: in expansion of macro 'MPL_ASSERT'  
   57 |     { MPL_ASSERT(( is_same< prior< WRAPPER(T,i) >::type, WRAPPER(T,i-1) > )); } \  
      |       ^~~~~~~~~~  
test/integral_wrapper_test.hpp:57:58: note: in expansion of macro 'WRAPPER'  
   57 |     { MPL_ASSERT(( is_same< prior< WRAPPER(T,i) >::type, WRAPPER(T,i-1) > )); } \  
      |                                                          ^~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:18:36: note: in expansion of macro 'INTEGRAL_WRAPPER_TEST'  
   18 | # define BOOST_PP_REPEAT_1_1(m, d) m(2, 0, d)  
      |                                    ^  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:19:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_1'  
   19 | # define BOOST_PP_REPEAT_1_2(m, d) BOOST_PP_REPEAT_1_1(m, d) m(2, 1, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:20:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_2'  
   20 | # define BOOST_PP_REPEAT_1_3(m, d) BOOST_PP_REPEAT_1_2(m, d) m(2, 2, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:21:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_3'  
   21 | # define BOOST_PP_REPEAT_1_4(m, d) BOOST_PP_REPEAT_1_3(m, d) m(2, 3, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:22:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_4'  
   22 | # define BOOST_PP_REPEAT_1_5(m, d) BOOST_PP_REPEAT_1_4(m, d) m(2, 4, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:23:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_5'  
   23 | # define BOOST_PP_REPEAT_1_6(m, d) BOOST_PP_REPEAT_1_5(m, d) m(2, 5, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:24:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_6'  
   24 | # define BOOST_PP_REPEAT_1_7(m, d) BOOST_PP_REPEAT_1_6(m, d) m(2, 6, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:25:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_7'  
   25 | # define BOOST_PP_REPEAT_1_8(m, d) BOOST_PP_REPEAT_1_7(m, d) m(2, 7, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:26:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_8'  
   26 | # define BOOST_PP_REPEAT_1_9(m, d) BOOST_PP_REPEAT_1_8(m, d) m(2, 8, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:27:37: note: in expansion of macro 'BOOST_PP_REPEAT_1_9'  
   27 | # define BOOST_PP_REPEAT_1_10(m, d) BOOST_PP_REPEAT_1_9(m, d) m(2, 9, d)  
      |                                     ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/repeat.hpp:44:39: note: in expansion of macro 'BOOST_PP_REPEAT_1_10'  
   44 | # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
      |                                       ^~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/repeat.hpp:39:37: note: in expansion of macro 'BOOST_PP_REPEAT_1_I'  
   39 | # define BOOST_PP_REPEAT_1(c, m, d) BOOST_PP_REPEAT_1_I(c, m, d)  
      |                                     ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
      |                                  ^  
../../boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
   22 | #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
      |                                ^~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/repeat.hpp:30:26: note: in expansion of macro 'BOOST_PP_CAT'  
   30 | # define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
      |                          ^~~~~~~~~~~~  
test/integral_c.cpp:25:5: note: in expansion of macro 'BOOST_PP_REPEAT'  
   25 |     BOOST_PP_REPEAT(10, INTEGRAL_WRAPPER_TEST, char)  
      |     ^~~~~~~~~~~~~~~  
test/integral_wrapper_test.hpp:57:73: error: template argument 2 is invalid  
   57 |     { MPL_ASSERT(( is_same< prior< WRAPPER(T,i) >::type, WRAPPER(T,i-1) > )); } \  
      |                                                                         ^  
../../boost/mpl/assert.hpp:60:58: note: in definition of macro 'BOOST_MPL_AUX_ASSERT_CONSTANT'  
   60 | #   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
      |                                                          ^~~~  
../../boost/mpl/aux_/test/assert.hpp:20:41: note: in expansion of macro 'BOOST_MPL_ASSERT'  
   20 | #define MPL_ASSERT(pred)                BOOST_MPL_ASSERT(pred)  
      |                                         ^~~~~~~~~~~~~~~~  
test/integral_wrapper_test.hpp:57:7: note: in expansion of macro 'MPL_ASSERT'  
   57 |     { MPL_ASSERT(( is_same< prior< WRAPPER(T,i) >::type, WRAPPER(T,i-1) > )); } \  
      |       ^~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:18:36: note: in expansion of macro 'INTEGRAL_WRAPPER_TEST'  
   18 | # define BOOST_PP_REPEAT_1_1(m, d) m(2, 0, d)  
      |                                    ^  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:19:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_1'  
   19 | # define BOOST_PP_REPEAT_1_2(m, d) BOOST_PP_REPEAT_1_1(m, d) m(2, 1, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:20:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_2'  
   20 | # define BOOST_PP_REPEAT_1_3(m, d) BOOST_PP_REPEAT_1_2(m, d) m(2, 2, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:21:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_3'  
   21 | # define BOOST_PP_REPEAT_1_4(m, d) BOOST_PP_REPEAT_1_3(m, d) m(2, 3, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:22:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_4'  
   22 | # define BOOST_PP_REPEAT_1_5(m, d) BOOST_PP_REPEAT_1_4(m, d) m(2, 4, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:23:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_5'  
   23 | # define BOOST_PP_REPEAT_1_6(m, d) BOOST_PP_REPEAT_1_5(m, d) m(2, 5, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:24:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_6'  
   24 | # define BOOST_PP_REPEAT_1_7(m, d) BOOST_PP_REPEAT_1_6(m, d) m(2, 6, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:25:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_7'  
   25 | # define BOOST_PP_REPEAT_1_8(m, d) BOOST_PP_REPEAT_1_7(m, d) m(2, 7, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:26:36: note: in expansion of macro 'BOOST_PP_REPEAT_1_8'  
   26 | # define BOOST_PP_REPEAT_1_9(m, d) BOOST_PP_REPEAT_1_8(m, d) m(2, 8, d)  
      |                                    ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/limits/repeat_256.hpp:27:37: note: in expansion of macro 'BOOST_PP_REPEAT_1_9'  
   27 | # define BOOST_PP_REPEAT_1_10(m, d) BOOST_PP_REPEAT_1_9(m, d) m(2, 9, d)  
      |                                     ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/repeat.hpp:44:39: note: in expansion of macro 'BOOST_PP_REPEAT_1_10'  
   44 | # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
      |                                       ^~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/repeat.hpp:39:37: note: in expansion of macro 'BOOST_PP_REPEAT_1_I'  
   39 | # define BOOST_PP_REPEAT_1(c, m, d) BOOST_PP_REPEAT_1_I(c, m, d)  
      |                                     ^~~~~~~~~~~~~~~~~~~  
../../boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
      |                                  ^  
../../boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
   22 | #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
      |                                ^~~~~~~~~~~~~~  
../../boost/preprocessor/repetition/repeat.hpp:30:26: note: in expansion of macro 'BOOST_PP_CAT'  
   30 | # define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
      |                          ^~~~~~~~~~~~  
test/integral_c.cpp:25:5: note: in expansion of macro 'BOOST_PP_REPEAT'  
   25 |     BOOST_PP_REPEAT(10, INTEGRAL_WRAPPER_TEST, char)  
      |     ^~~~~~~~~~~~~~~  
../../boost/mpl/assert.hpp: In instantiation of 'struct mpl_::assert_arg_pred_not<int>':  
../../boost/mpl/assert.hpp:210:1:   required by substitution of 'template<class Pred> mpl_::assert<false> mpl_::assert_arg(void (*)(Pred), typename mpl_::assert_arg_pred_not<Pred>::type) [with Pred = int]'  
test/integral_c.cpp:25:5:   required from here  
../../boost/mpl/assert.hpp:182:30: error: 'int' is not a class, struct, or union type  
  182 |     typedef typename P::type p_type;  
      |                              ^~~~~~  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -Wextra -Wno-variadic-macros -Wshadow -DBOOST_ALL_NO_LIB=1  -I"../.."  -c -o "../../bin.v2/libs/mpl/test/integral_c.test/gcc-11/debug/threading-multi/visibility-hidden/integral_c.o" "test/integral_c.cpp"  
  
...failed gcc.compile.c++ ../../bin.v2/libs/mpl/test/integral_c.test/gcc-11/debug/threading-multi/visibility-hidden/integral_c.o...  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2022-04-30 21:18:28 UTC  
> Url: https://github.com/boostorg/mpl/issues/64#issuecomment-1114055697  

Duplicate of #50
