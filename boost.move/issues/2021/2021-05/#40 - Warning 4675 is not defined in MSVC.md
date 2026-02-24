# #40 - Warning 4675 is not defined in MSVC [Closed]

> Username: Guillaumebeuzeboc  
> Created at: 2021-05-17 09:16:27 UTC  
> Updated at: 2021-06-19 18:20:49 UTC  
> Closed at: 2021-06-19 18:20:49 UTC  
> Url: https://github.com/boostorg/move/issues/40  

The warning 4675 is disabled here: https://github.com/boostorg/move/blob/develop/include/boost/move/detail/config_begin.hpp#L17  
  
But this warning doesn't exist in MSVC (tested on Win10 with MSVC v14.28.29910).   
Thus, it generates the warning C4619: `#pragma warning : there is no warning number '4675'`  
If the project activate warning as errors it prevents the compilation

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-06-19 18:20:06 UTC  
> Url: https://github.com/boostorg/move/issues/40#issuecomment-864445426  

Looks like the warning was removed in recent compilers. Following:  
  
https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-1-c4179?view=msvc-160  
  
we'll disable 4619 warning until all supported compilers don't allow 4675.
