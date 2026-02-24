# #7 - C++11 enabled BOOST_SCOPE_EXIT fails to compile with gcc 4.8.5 [Open]

> Username: p-pautov  
> Created at: 2018-08-13 20:25:44 UTC  
> Updated at: 2021-11-10 21:38:01 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/7  

```C++  
#define BOOST_SCOPE_EXIT_CONFIG_USE_LAMBDAS  
#include <boost/scope_exit.hpp>  
  
int main() {  
    int test = 1;  
    BOOST_SCOPE_EXIT(&) {  
        test = 2;  
    };  
}  
```  
  
`g++ scope_exit.cpp -std=c++11 -Iboost_1_68_0` produces following error for program above:  
  
```  
In file included from boost_1_68_0/boost/mpl/aux_/integral_wrapper.hpp:22:0,  
                 from boost_1_68_0/boost/mpl/int.hpp:20,  
                 from boost_1_68_0/boost/mpl/lambda_fwd.hpp:23,  
                 from boost_1_68_0/boost/mpl/aux_/na_spec.hpp:18,  
                 from boost_1_68_0/boost/mpl/not.hpp:20,  
                 from boost_1_68_0/boost/mpl/assert.hpp:17,  
                 from boost_1_68_0/boost/scope_exit.hpp:15,  
                 from scope_exit.cpp:2:  
scope_exit.cpp:6:22: error: pasting "&" and "_BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_VOID_IS" does not give a valid preprocessing token  
     BOOST_SCOPE_EXIT(&) {  
                      ^  
boost_1_68_0/boost/preprocessor/cat.hpp:29:34: note: in definition of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
boost_1_68_0/boost/scope_exit.hpp:85:23: note: in expansion of macro 'BOOST_PP_CAT'  
     BOOST_PP_IS_UNARY(BOOST_PP_CAT(token, checking_postfix))  
                       ^  
boost_1_68_0/boost/scope_exit.hpp:92:5: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_IS_BACK_'  
     BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_IS_BACK_(token, \  
     ^  
boost_1_68_0/boost/scope_exit.hpp:111:18: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_IS_VOID_BACK'  
     BOOST_PP_IIF(is_void_macro(token), \  
                  ^  
boost_1_68_0/boost/preprocessor/control/iif.hpp:31:31: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_HANDLE_VOID_'  
 # define BOOST_PP_IIF_0(t, f) f  
                               ^  
boost_1_68_0/boost/preprocessor/control/iif.hpp:32:31: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_HANDLE_SEQ_'  
 # define BOOST_PP_IIF_1(t, f) t  
                               ^  
boost_1_68_0/boost/scope_exit.hpp:177:5: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_'  
     BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_( \  
     ^  
boost_1_68_0/boost/scope_exit.hpp:893:25: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST'  
                         BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST(__VA_ARGS__)))  
                         ^  
boost_1_68_0/boost/scope_exit.hpp:899:9: note: in expansion of macro 'BOOST_SCOPE_EXIT_ID'  
         BOOST_SCOPE_EXIT_ID(BOOST_SCOPE_EXIT_AUX_PP_LINE_COUNTER, \  
         ^  
scope_exit.cpp:6:5: note: in expansion of macro 'BOOST_SCOPE_EXIT'  
     BOOST_SCOPE_EXIT(&) {  
     ^  
scope_exit.cpp:6:22: error: pasting "&" and "BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_THISUNDERSCORE_IS" does not give a valid preprocessing token  
     BOOST_SCOPE_EXIT(&) {  
                      ^  
boost_1_68_0/boost/preprocessor/cat.hpp:29:34: note: in definition of macro 'BOOST_PP_CAT_I'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
boost_1_68_0/boost/scope_exit.hpp:85:23: note: in expansion of macro 'BOOST_PP_CAT'  
     BOOST_PP_IS_UNARY(BOOST_PP_CAT(token, checking_postfix))  
                       ^  
boost_1_68_0/boost/scope_exit.hpp:88:5: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_IS_BACK_'  
     BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_IS_BACK_(token, \  
     ^  
boost_1_68_0/boost/scope_exit.hpp:667:18: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_IS_THISUNDERSCORE_BACK'  
     BOOST_PP_IIF(BOOST_SCOPE_EXIT_AUX_PP_KEYWORD_IS_THISUNDERSCORE_BACK(\  
                  ^  
boost_1_68_0/boost/preprocessor/control/expr_iif.hpp:29:36: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_TRAITS_OP'  
 # define BOOST_PP_EXPR_IIF_1(expr) expr  
                                    ^  
boost_1_68_0/boost/preprocessor/list/adt.hpp:35:41: note: in expansion of macro 'BOOST_PP_LIST_FIRST_I'  
 #    define BOOST_PP_LIST_FIRST_D(list) BOOST_PP_LIST_FIRST_I list  
                                         ^  
boost_1_68_0/boost/preprocessor/list/adt.hpp:32:36: note: in expansion of macro 'BOOST_PP_LIST_FIRST_D'  
 # define BOOST_PP_LIST_FIRST(list) BOOST_PP_LIST_FIRST_D(list)  
                                    ^  
boost_1_68_0/boost/preprocessor/list/detail/fold_left.hpp:22:191: note: in expansion of macro 'BOOST_PP_LIST_FIRST'  
 # define BOOST_PP_LIST_FOLD_LEFT_1(o, s, l) BOOST_PP_IIF(BOOST_PP_LIST_IS_CONS(l), BOOST_PP_LIST_FOLD_LEFT_2, s BOOST_PP_TUPLE_EAT_3)(o, BOOST_PP_EXPR_IIF(BOOST_PP_LIST_IS_CONS(l), o)(2, s, BOOST_PP_LIST_FIRST(l)), BOOST_PP_LIST_REST(l))  
                                                                                                                                                                                               ^  
boost_1_68_0/boost/preprocessor/cat.hpp:29:34: note: in expansion of macro 'BOOST_PP_LIST_FOLD_LEFT_1'  
 #    define BOOST_PP_CAT_I(a, b) a ## b  
                                  ^  
boost_1_68_0/boost/scope_exit.hpp:892:17: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_TRAITS'  
                 BOOST_SCOPE_EXIT_AUX_TRAITS( \  
                 ^  
boost_1_68_0/boost/preprocessor/control/iif.hpp:25:39: note: in expansion of macro 'BOOST_PP_IIF_0'  
 #    define BOOST_PP_IIF_I(bit, t, f) BOOST_PP_IIF_ ## bit(t, f)  
                                       ^  
boost_1_68_0/boost/preprocessor/control/iif.hpp:18:37: note: in expansion of macro 'BOOST_PP_IIF_I'  
 #    define BOOST_PP_IIF(bit, t, f) BOOST_PP_IIF_I(bit, t, f)  
                                     ^  
boost_1_68_0/boost/scope_exit.hpp:111:5: note: in expansion of macro 'BOOST_PP_IIF'  
     BOOST_PP_IIF(is_void_macro(token), \  
     ^  
boost_1_68_0/boost/preprocessor/control/iif.hpp:31:31: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_HANDLE_VOID_'  
 # define BOOST_PP_IIF_0(t, f) f  
                               ^  
boost_1_68_0/boost/preprocessor/control/iif.hpp:32:31: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_HANDLE_SEQ_'  
 # define BOOST_PP_IIF_1(t, f) t  
                               ^  
boost_1_68_0/boost/scope_exit.hpp:177:5: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_'  
     BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST_( \  
     ^  
boost_1_68_0/boost/scope_exit.hpp:893:25: note: in expansion of macro 'BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST'  
                         BOOST_SCOPE_EXIT_AUX_PP_VOID_LIST(__VA_ARGS__)))  
                         ^  
boost_1_68_0/boost/scope_exit.hpp:899:9: note: in expansion of macro 'BOOST_SCOPE_EXIT_ID'  
         BOOST_SCOPE_EXIT_ID(BOOST_SCOPE_EXIT_AUX_PP_LINE_COUNTER, \  
         ^  
scope_exit.cpp:6:5: note: in expansion of macro 'BOOST_SCOPE_EXIT'  
     BOOST_SCOPE_EXIT(&) {  
```

---

## Comment 1

> Username: MattStephanson  
> Created at: 2021-11-10 21:38:01 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/7#issuecomment-965768179  

I just encountered the same error, and this issue was one of the few search results. For anyone seeing the same thing, I think the fix is to use `BOOST_SCOPE_EXIT_ALL`, not `BOOST_SCOPE_EXIT`, when using a default capture.
