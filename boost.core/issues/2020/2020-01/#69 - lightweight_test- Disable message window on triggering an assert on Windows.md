# #69 - lightweight_test: Disable message window on triggering an assert on Windows [Closed]

> Username: HDembinski  
> Created at: 2020-01-01 12:22:09 UTC  
> Updated at: 2020-03-16 13:49:46 UTC  
> Closed at: 2020-03-16 13:49:46 UTC  
> Url: https://github.com/boostorg/core/issues/69  

At least in VS 2015, the c++ stdlib reports violated asserts with a message box prompted to the user. This breaks CI services such as Appveyor, which only show the terminal output and do not allow one to react to the message box. From the user point of view, the build simply stalls for no reason at some point in time without any message as to why.  
  
As Peter pointed out, the issue can be fixed by calling these two functions before the code trips any assert:  
```  
_CrtSetReportMode( _CRT_ASSERT, _CRTDBG_MODE_FILE );  
_CrtSetReportFile( _CRT_ASSERT, _CRTDBG_FILE_STDERR );  
```  
See:  
https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros?view=vs-2019  
https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/crtsetreportmode?view=vs-2019  
  
How to handle this?  
  
I think this should be set automatically when lightweight_test is used. The class `test_result` has some code to change the abort behaviour already. However, this change is triggered only when `test_result` is first used, which is on the first `BOOST_TEST` call. The assert may be triggered before. Another option is to use some code like this somewhere in the detail namespace of lightweight_test.hpp  
```c++  
#if defined(_MSC_VER) && (_MSC_VER > SOME_VERSION)  
struct assert_window_disabler_t {  
  assert_window_disabler_t() {  
    _CrtSetReportMode( _CRT_ASSERT, _CRTDBG_MODE_FILE );  
    _CrtSetReportFile( _CRT_ASSERT, _CRTDBG_FILE_STDERR );  
  }  
} assert_window_disabler_;  
#endif  
```  
The complicated initialization order of globals may be an issue.  
https://stackoverflow.com/questions/3746238/c-global-initialization-order-ignores-dependencies/3746249#3746249

---

## Comment 1

> Username: mloskot  
> Created at: 2020-03-15 21:15:48 UTC  
> Url: https://github.com/boostorg/core/issues/69#issuecomment-599265739  

This change by @pdimov in https://github.com/boostorg/core/commit/bda2b9b4b1064e578c518609248385676db410a7 seems to address this problem
