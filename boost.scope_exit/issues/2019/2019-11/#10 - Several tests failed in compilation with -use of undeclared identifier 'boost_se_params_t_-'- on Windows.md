# #10 - Several tests failed in compilation with "use of undeclared identifier 'boost_se_params_t_*'" on Windows [Open]

> Username: yuxianch  
> Created at: 2019-11-18 06:33:21 UTC  
> Updated at: 2019-11-18 06:33:21 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/10  

Hi,  
I found that several tests failed in compilation on Windows, which was caused by using undeclared identifier "boost_se_params_t_*". Take world_tpl_seq.cpp as an example.  
When executing below command,  
`clang-cl -m32 world_tpl_seq.cpp -o world_tpl_seq -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++17 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -I"..\..\.."`  
I got the following error message.  
```  
world_tpl_seq.cpp(34,5): error: use of undeclared identifier 'boost_se_params_t_34'  
    BOOST_SCOPE_EXIT_TPL( (&commit) (this_) ) {  
    ^  
..\..\..\boost/scope_exit.hpp(901,9): note: expanded from macro 'BOOST_SCOPE_EXIT_TPL'  
        BOOST_SCOPE_EXIT_ID_TPL(BOOST_SCOPE_EXIT_AUX_PP_LINE_COUNTER, \  
        ^  
..\..\..\boost/scope_exit.hpp(894,9): note: expanded from macro 'BOOST_SCOPE_EXIT_ID_TPL'  
        BOOST_SCOPE_EXIT_AUX_IMPL(id, typename, \  
        ^  
..\..\..\boost/scope_exit.hpp(828,22): note: expanded from macro 'BOOST_SCOPE_EXIT_AUX_IMPL'  
                    (BOOST_SCOPE_EXIT_DETAIL_PARAMS_T(id)*)boost_se_params) \  
                     ^  
note: (skipping 1 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
..\..\..\boost/preprocessor/cat.hpp(22,32): note: expanded from macro 'BOOST_PP_CAT'  
#    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
                               ^  
..\..\..\boost/preprocessor/cat.hpp(29,34): note: expanded from macro 'BOOST_PP_CAT_I'  
#    define BOOST_PP_CAT_I(a, b) a ## b  
                                 ^  
<scratch space>(6,1): note: expanded from here  
boost_se_params_t_34  
^  
world_tpl_seq.cpp(46,7): note: in instantiation of member function 'world<person>::add_person' requested here  
    w.add_person(p);  
      ^  
world_tpl_seq.cpp(34,5): error: expected expression  
    BOOST_SCOPE_EXIT_TPL( (&commit) (this_) ) {  
    ^  
..\..\..\boost/scope_exit.hpp(901,9): note: expanded from macro 'BOOST_SCOPE_EXIT_TPL'  
        BOOST_SCOPE_EXIT_ID_TPL(BOOST_SCOPE_EXIT_AUX_PP_LINE_COUNTER, \  
        ^  
..\..\..\boost/scope_exit.hpp(894,9): note: expanded from macro 'BOOST_SCOPE_EXIT_ID_TPL'  
        BOOST_SCOPE_EXIT_AUX_IMPL(id, typename, \  
        ^  
..\..\..\boost/scope_exit.hpp(828,59): note: expanded from macro 'BOOST_SCOPE_EXIT_AUX_IMPL'  
                    (BOOST_SCOPE_EXIT_DETAIL_PARAMS_T(id)*)boost_se_params) \  
                                                          ^  
2 errors generated.  
```  
From above message we are told that the compiler can not find the declaration of `boost_se_params_t_34`. And I did not find where it is defined as well. Is it declared somewhere?
