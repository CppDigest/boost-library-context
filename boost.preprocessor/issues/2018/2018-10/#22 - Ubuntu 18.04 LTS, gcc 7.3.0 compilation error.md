# #22 - Ubuntu 18.04 LTS, gcc 7.3.0 compilation error [Closed]

> Username: nynorbert  
> Created at: 2018-10-10 13:31:14 UTC  
> Updated at: 2018-10-19 20:24:58 UTC  
> Closed at: 2018-10-19 20:24:58 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/22  

Hi,  
  
We are working on a project based on Boost and our code is not compiling on Ubuntu 18.04 with gcc 7.3.0 but it compiles on Ubuntu 16.04.5 with gcc 5.4.0. I have tried with different boost versions from 1.60 to 1.68 but I got the same error message.   
I also tried to compile the code snippet below with "-std=c++98" gcc switch and in that case I successfully compiled it but other parts of our code is using c++11 so it is not a good solution for us.  
Do you have any idea what causes the problem?  
  
**The minimal example that fails to compile:**  
  
```  
#include <boost/variant/variant.hpp>  
#include <boost/variant/variant_fwd.hpp>  
#include <boost/preprocessor/repetition/repeat.hpp>  
#include <boost/preprocessor/repetition/enum_params.hpp>  
#include <boost/preprocessor/repetition/enum.hpp>  
  
namespace serialization {  
//...  
#if !defined(BOOST_NO_USING_DECLARATION_OVERLOADS_FROM_TYPENAME_BASE)  
        #define XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS_IMPL(z, N, T) \  
                typename BOOST_PP_CAT(T,N) = boost::detail::variant::void_ \  
                /**/  
  
#else  
        #define XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS_IMPL(z, N, T) \  
                typename BOOST_PP_CAT(T,N) = BOOST_PP_CAT(boost::detail::variant::void,N) \  
                /**/  
#endif  
        #define XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS \  
                BOOST_PP_ENUM( \  
                          BOOST_PP_SUB(BOOST_VARIANT_LIMIT_TYPES, 1) \  
                        , XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS_IMPL \  
                        , T \  
                        ) \  
                /**/  
        template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
        struct xml_mixed_choice_schema {  
            int a;  
        };  
//...  
}  
```  
  
**The compilation error:**  
  
```  
In file included from /home/ttcn/boost_install//include/boost/preprocessor/repeat.hpp:15:0,  
                 from /home/ttcn/boost_install//include/boost/mpl/aux_/preprocessor/params.hpp:46,  
                 from /home/ttcn/boost_install//include/boost/mpl/aux_/na_spec.hpp:26,  
                 from /home/ttcn/boost_install//include/boost/mpl/not.hpp:20,  
                 from /home/ttcn/boost_install//include/boost/mpl/assert.hpp:17,  
                 from /home/ttcn/boost_install//include/boost/mpl/aux_/na_assert.hpp:23,  
                 from /home/ttcn/boost_install//include/boost/mpl/arg.hpp:25,  
                 from /home/ttcn/boost_install//include/boost/variant/variant_fwd.hpp:19,  
                 from /home/ttcn/boost_install//include/boost/variant/variant.hpp:26,  
                 from minimal.cpp:2:  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:43:39: error: 'BOOST_PP_REPEAT_1_BOOST_PP_DEC_BOOST_VARIANT_LIMIT_TYPES' has not been declared  
 # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                       ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:38:37: note: in expansion of macro 'BOOST_PP_REPEAT_1_I'  
 # define BOOST_PP_REPEAT_1(c, m, d) BOOST_PP_REPEAT_1_I(c, m, d)  
                                     ^~~~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:38: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                      ^~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_ENUM_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                                ^~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:32:24: note: in expansion of macro 'BOOST_PP_CAT'  
 # define BOOST_PP_ENUM BOOST_PP_CAT(BOOST_PP_ENUM_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                        ^~~~~~~~~~~~  
minimal.cpp:21:3: note: in expansion of macro 'BOOST_PP_ENUM'  
   BOOST_PP_ENUM( \  
   ^~~~~~~~~~~~~  
minimal.cpp:27:11: note: in expansion of macro 'XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS'  
  template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
           ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:43:64: error: expected ')' before ',' token  
 # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                                                ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:38:37: note: in expansion of macro 'BOOST_PP_REPEAT_1_I'  
 # define BOOST_PP_REPEAT_1(c, m, d) BOOST_PP_REPEAT_1_I(c, m, d)  
                                     ^~~~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:38: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                      ^~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_ENUM_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                                ^~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:32:24: note: in expansion of macro 'BOOST_PP_CAT'  
 # define BOOST_PP_ENUM BOOST_PP_CAT(BOOST_PP_ENUM_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                        ^~~~~~~~~~~~  
minimal.cpp:21:3: note: in expansion of macro 'BOOST_PP_ENUM'  
   BOOST_PP_ENUM( \  
   ^~~~~~~~~~~~~  
minimal.cpp:27:11: note: in expansion of macro 'XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS'  
  template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
           ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:78: error: expected identifier before '(' token  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                                                              ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:43:66: note: in definition of macro 'BOOST_PP_REPEAT_1_I'  
 # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:38: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                      ^~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_ENUM_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                                ^~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:32:24: note: in expansion of macro 'BOOST_PP_CAT'  
 # define BOOST_PP_ENUM BOOST_PP_CAT(BOOST_PP_ENUM_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                        ^~~~~~~~~~~~  
minimal.cpp:21:3: note: in expansion of macro 'BOOST_PP_ENUM'  
   BOOST_PP_ENUM( \  
   ^~~~~~~~~~~~~  
minimal.cpp:27:11: note: in expansion of macro 'XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS'  
  template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
           ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:80: error: expected ')' before ',' token  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                                                                ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:43:66: note: in definition of macro 'BOOST_PP_REPEAT_1_I'  
 # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:38: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                      ^~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_ENUM_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                                ^~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:32:24: note: in expansion of macro 'BOOST_PP_CAT'  
 # define BOOST_PP_ENUM BOOST_PP_CAT(BOOST_PP_ENUM_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                        ^~~~~~~~~~~~  
minimal.cpp:21:3: note: in expansion of macro 'BOOST_PP_ENUM'  
   BOOST_PP_ENUM( \  
   ^~~~~~~~~~~~~  
minimal.cpp:27:11: note: in expansion of macro 'XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS'  
  template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
           ^  
minimal.cpp:24:6: error: 'T' has not been declared  
    , T \  
      ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:43:66: note: in definition of macro 'BOOST_PP_REPEAT_1_I'  
 # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:38: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                      ^~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_ENUM_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
minimal.cpp:27:11: note: in expansion of macro 'XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS'  
  template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
           ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:83: error: expected '>' before ')' token  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                                                                   ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:43:66: note: in definition of macro 'BOOST_PP_REPEAT_1_I'  
 # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:38: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                      ^~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_ENUM_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                                ^~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:32:24: note: in expansion of macro 'BOOST_PP_CAT'  
 # define BOOST_PP_ENUM BOOST_PP_CAT(BOOST_PP_ENUM_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                        ^~~~~~~~~~~~  
minimal.cpp:21:3: note: in expansion of macro 'BOOST_PP_ENUM'  
   BOOST_PP_ENUM( \  
   ^~~~~~~~~~~~~  
minimal.cpp:27:11: note: in expansion of macro 'XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS'  
  template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
           ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:83: error: expected unqualified-id before ')' token  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                                                                   ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/repeat.hpp:43:66: note: in definition of macro 'BOOST_PP_REPEAT_1_I'  
 # define BOOST_PP_REPEAT_1_I(c, m, d) BOOST_PP_REPEAT_1_ ## c(m, d)  
                                                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:35:38: note: in expansion of macro 'BOOST_PP_REPEAT_1'  
 #    define BOOST_PP_ENUM_1(c, m, d) BOOST_PP_REPEAT_1(c, BOOST_PP_ENUM_M_1, (m, d))  
                                      ^~~~~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_ENUM_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
/home/ttcn/boost_install//include/boost/preprocessor/cat.hpp:22:32: note: in expansion of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                                ^~~~~~~~~~~~~~  
/home/ttcn/boost_install//include/boost/preprocessor/repetition/enum.hpp:32:24: note: in expansion of macro 'BOOST_PP_CAT'  
 # define BOOST_PP_ENUM BOOST_PP_CAT(BOOST_PP_ENUM_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
                        ^~~~~~~~~~~~  
minimal.cpp:21:3: note: in expansion of macro 'BOOST_PP_ENUM'  
   BOOST_PP_ENUM( \  
   ^~~~~~~~~~~~~  
minimal.cpp:27:11: note: in expansion of macro 'XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS'  
  template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
```

---

## Comment 1

> Username: eldiener  
> Created at: 2018-10-15 15:18:24 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/22#issuecomment-429896370  

Please try adding these header files:  
  
```  
#include <boost/preprocessor/arithmetic/sub.hpp>  
#include <boost/preprocessor/cat.hpp>  
```  
  
These more simplified examples worked for me:  
  
```  
#include <boost/preprocessor/repetition/enum.hpp>  
#include <boost/preprocessor/arithmetic/sub.hpp>  
#include <boost/preprocessor/cat.hpp>  
  
           #define XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS_IMPL(z, N, T) \  
                   typename BOOST_PP_CAT(T,N) = BOOST_PP_CAT(boost::detail::variant::void,N) \  
                   /**/  
  
          #define XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS \  
                  BOOST_PP_ENUM( \  
                            BOOST_PP_SUB(20, 1) \  
                          , XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS_IMPL \  
                          , T \  
                          ) \  
                  /**/  
  
        template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
        struct xml_mixed_choice_schema {  
            int a;  
        };  
  
```  
and:  
  
```  
#include <boost/preprocessor/repetition/enum.hpp>  
#include <boost/preprocessor/arithmetic/sub.hpp>  
#include <boost/preprocessor/cat.hpp>  
  
        #define XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS_IMPL(z, N, T) \  
                typename BOOST_PP_CAT(T,N) = boost::detail::variant::void_ \  
                /**/  
  
          #define XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS \  
                  BOOST_PP_ENUM( \  
                            BOOST_PP_SUB(20, 1) \  
                          , XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS_IMPL \  
                          , T \  
                          ) \  
                  /**/  
        template<XML_MIXED_DATA_TYPE_AUX_DECLARE_PARAMS>  
        struct xml_mixed_choice_schema {  
            int a;  
        };  
  
```
