# #428 - Warnings on Windows Clang in header code [Open]

> Username: correaa  
> Created at: 2024-07-23 21:41:45 UTC  
> Updated at: 2024-07-23 21:41:45 UTC  
> Url: https://github.com/boostorg/test/issues/428  

when using Boost.Test in header mode I get these warnings:  
  
https://github.com/correaa/boost-multi/actions/runs/10066511702/job/27828068311#step:4:119  
```  
.\boost/test/utils/setcolor.hpp(32,12): warning: non-portable path to file '<Windows.h>'; specified path differs in case from file name on disk [-Wnonportable-system-include-path]  
   32 |   #include <windows.h>  
      |            ^~~~~~~~~~~  
      |            <Windows.h>  
.\boost/test/utils/setcolor.hpp(214,5): warning: default label in switch which covers all enumeration values [-Wcovered-switch-default]  
  214 |     default:  
      |     ^  
.\boost/test/utils/setcolor.hpp(220,12): warning: enumeration values 'YELLOW', 'MAGENTA', and 'CYAN' not explicitly handled in switch [-Wswitch-enum]  
  220 |     switch(m_bg)  
      |            ^~~~  
.\boost/test/utils/setcolor.hpp(244,12): warning: 5 enumeration values not explicitly handled in switch: 'NORMAL', 'DIM', 'BLINK'... [-Wswitch-enum]  
  244 |     switch(m_attr)  
      |            ^~~~~~  
In file included from libs\boost-multi\test\array_cref.cpp:27:  
In file included from .\boost/test/included/unit_test.hpp:18:  
In file included from .\boost/test/impl/compiler_log_formatter.ipp:23:  
In file included from .\boost/test/unit_test_parameters.hpp:19:  
In file included from .\boost/test/utils/runtime/argument.hpp:20:  
.\boost/test/utils/runtime/errors.hpp(60,37): warning: parameter 'param_name' shadows member inherited from type 'param_error' [-Wshadow-field]  
   60 |     explicit    init_error( cstring param_name ) : param_error( param_name ) {}  
      |                                     ^  
.\boost/test/utils/runtime/errors.hpp(49,17): note: declared here  
   49 |     cstring     param_name;  
      |                 ^  
.\boost/test/utils/runtime/errors.hpp(66,38): warning: parameter 'param_name' shadows member inherited from type 'param_error' [-Wshadow-field]  
   66 |     explicit    input_error( cstring param_name ) : param_error( param_name ) {}  
      |                                      ^  
.\boost/test/utils/runtime/errors.hpp(49,17): note: declared here  
   49 |     cstring     param_name;  
      |                 ^  
.\boost/test/utils/runtime/errors.hpp(144,1): warning: parameter 'param_name' shadows member inherited from type 'param_error' [-Wshadow-field]  
  144 | SPECIFIC_EX_TYPE( invalid_cla_id, init_error );  
   49 |     cstring     param_name;  
      |                 ^  
.\boost/test/utils/runtime/errors.hpp(150,1): warning: parameter 'param_name' shadows member inherited from type 'param_error' [-Wshadow-field]  
  150 | SPECIFIC_EX_TYPE( invalid_param_spec, init_error );  
      | ^  
.\boost/test/utils/runtime/errors.hpp(138,28): note: expanded from macro 'SPECIFIC_EX_TYPE'  
  138 |     explicit type( cstring param_name = cstring() )     \  
      |                            ^  
.\boost/test/utils/runtime/errors.hpp(49,17): note: declared here  
   49 |     cstring     param_name;  
      |                 ^  
.\boost/test/utils/runtime/errors.hpp(152,1): warning: parameter 'param_name' shadows member inherited from type 'param_error' [-Wshadow-field]  
  152 | SPECIFIC_EX_TYPE( format_error, input_error );  
      | ^  
.\boost/test/utils/runtime/errors.hpp(138,28): note: expanded from macro 'SPECIFIC_EX_TYPE'  
  138 |     explicit type( cstring param_name = cstring() )     \  
      |                            ^  
.\boost/test/utils/runtime/errors.hpp(49,17): note: declared here  
   49 |     cstring     param_name;  
      |                 ^  
.\boost/test/utils/runtime/errors.hpp(153,1): warning: parameter 'param_name' shadows member inherited from type 'param_error' [-Wshadow-field]  
  153 | SPECIFIC_EX_TYPE( duplicate_arg, input_error );  
      | ^  
.\boost/test/utils/runtime/errors.hpp(138,28): note: expanded from macro 'SPECIFIC_EX_TYPE'  
  138 |     explicit type( cstring param_name = cstring() )     \  
      |                            ^  
.\boost/test/utils/runtime/errors.hpp(49,17): note: declared here  
   49 |     cstring     param_name;  
      |                 ^  
.\boost/test/utils/runtime/errors.hpp(154,1): warning: parameter 'param_name' shadows member inherited from type 'param_error' [-Wshadow-field]  
  154 | SPECIFIC_EX_TYPE( missing_req_arg, input_error );  
      | ^  
.\boost/test/utils/runtime/errors.hpp(138,28): note: expanded from macro 'SPECIFIC_EX_TYPE'  
  138 |     explicit type( cstring param_name = cstring() )     \  
      |                            ^  
.\boost/test/utils/runtime/errors.hpp(49,17): note: declared here  
   49 |     cstring     param_name;  
      |                 ^  
In file included from libs\boost-multi\test\array_cref.cpp:27:  
In file included from .\boost/test/included/unit_test.hpp:19:  
.\boost/test/impl/junit_log_formatter.ipp(610,12): warning: enumeration value 'no_error' not explicitly handled in switch [-Wswitch-enum]  
  610 |     switch(ex.code())  
      |            ^~~~~~~~~  
.\boost/test/impl/junit_log_formatter.ipp(722,7): warning: default label in switch which covers all enumeration values [-Wcovered-switch-default]  
  722 |       default:  
      |       ^  
```
