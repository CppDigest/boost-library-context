# #308 - Report tests as failed if Memory Leak is detected [Open]

> Username: dmenendez-gruposantander  
> Created at: 2021-04-13 14:42:22 UTC  
> Updated at: 2021-04-13 14:42:22 UTC  
> Url: https://github.com/boostorg/test/issues/308  

Currently using `--detect_memory_leaks` [param](https://www.boost.org/doc/libs/1_75_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/detect_memory_leaks.html) I can get a Memory Leak report as shown [here](https://docs.microsoft.com/en-us/visualstudio/debugger/finding-memory-leaks-using-the-crt-library?view=vs-2019)  
  
However, even if there have been memory leaks, the overall test suite status (returned from main) is success.  
  
What I'd really need is that the Boost.Test machinery returns a failure from main when a memory leak is detected, so that the test suite is considered failed. This is for running unit-tests as a post-build event or under CI.  
  
Is that possible using Boost.Test?
