# #230 - boost/1.85.0 compile issues with msm::front::state_machine_def on llvm/clang++ 18.1.5 with std=c++20 [Closed]

> Username: hoyhoy  
> Created at: 2024-05-04 20:43:07 UTC  
> Updated at: 2024-05-05 17:45:59 UTC  
> Closed at: 2024-05-04 21:19:28 UTC  
> Url: https://github.com/boostorg/log/issues/230  

We have some code that uses  
  
```  
struct front_msm : msm::front::state_machine_def<front_msm, visitable_state> {...}  
struct visitable_state {}  
typedef boost::msm::back::recursive_get_transition_table<front_msm>::type recursive_stt;  
  
typedef boost::msm::back::recursive_get_transition_table<Sm_rund_front>::type recursive_stt;  
typedef boost::msm::back::generate_state_set<recursive_stt>::type all_states;  
```  
  
This is erroring out with boost 1.85.0 on the latest clang.  boost 1.84.0 compiles successfully though.  
  
clang++ 18.1.5 errors out with this on the compile.  
  
```  
/include/boost/msm/back/args.hpp:60:45: error: 'no_args' does not refer to a value [Semantic Issue]  
   60 | BOOST_PP_REPEAT(BOOST_MSM_VISITOR_ARG_SIZE, MSM_VISITOR_ARGS, ~)  
      |                                             ^  
/include/boost/preprocessor/repetition/repeat.hpp:30:26: note: expanded from macro 'BOOST_PP_REPEAT'  
   30 | # define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
      |                          ^  
/include/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
   22 | #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
      |                                ^  
/include/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
      |                                  ^  
<scratch space>:49:1: note: expanded from here  
   49 | BOOST_PP_REPEAT_1  
      | ^  
/include/boost/msm/back/args.hpp:31:8: note: declared here  
   31 | struct no_args {};  
      |        ^  
/include/boost/msm/back/args.hpp:60:45: error: use of undeclared identifier 'MSM_ARGS_PRINT' [Semantic Issue]  
   60 | BOOST_PP_REPEAT(BOOST_MSM_VISITOR_ARG_SIZE, MSM_VISITOR_ARGS, ~)  
      |                                             ^  
/include/boost/preprocessor/repetition/repeat.hpp:30:26: note: expanded from macro 'BOOST_PP_REPEAT'  
   30 | # define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
      |                          ^  
/include/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
   22 | #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
      |                                ^  
/include/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
      |                                  ^  
<scratch space>:49:1: note: expanded from here  
   49 | BOOST_PP_REPEAT_1  
      | ^  
```

---

## Comment 1

> Username: hoyhoy  
> Created at: 2024-05-04 20:47:36 UTC  
> Updated at: 2024-05-04 20:48:07 UTC  
> Url: https://github.com/boostorg/log/issues/230#issuecomment-2094375099  

msvc 17.9.4 had problems with this code on boost 1.84.0 as well, but that seemed to get fixed at 17.9.6.   
  https://developercommunity.visualstudio.com/t/cl-193933523-x64-boost1840-boost/10622335

---

## Comment 2

> Username: Lastique  
> Created at: 2024-05-04 21:19:29 UTC  
> Url: https://github.com/boostorg/log/issues/230#issuecomment-2094400314  

Please, report this to [Boost.MSM](https://github.com/boostorg/msm/issues).

---

## Comment 3

> Username: hoyhoy  
> Created at: 2024-05-05 17:13:16 UTC  
> Url: https://github.com/boostorg/log/issues/230#issuecomment-2094881898  

Seems like `args.hpp` is missing `#include <boost/preprocessor/repetition/enum.hpp>`

---

## Comment 4

> Username: hoyhoy  
> Created at: 2024-05-05 17:45:58 UTC  
> Url: https://github.com/boostorg/log/issues/230#issuecomment-2094891124  

Even with the include our ` boost::msm::back::state_machine` is now going into an infinite recursion and crashing.  Worked in boost/1.84.0
