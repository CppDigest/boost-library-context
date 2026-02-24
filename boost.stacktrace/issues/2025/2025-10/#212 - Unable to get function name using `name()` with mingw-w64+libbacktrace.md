# #212 - Unable to get function name using `name()` with mingw-w64+libbacktrace [Open]

> Username: prbegd  
> Created at: 2025-10-04 12:09:31 UTC  
> Updated at: 2026-01-24 11:09:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/212  

Hi,  
  
I'm  building with mingw-w64 on Windows and using libbacktrace as implementation. I try to get function names using `name()` on a frame, but I got `register_frame_ctor`. But other functions like `address()`,, `source_file()` and `source_line()` are working correctly.  
  
## Reproducing  
Here is an example:  
```cpp  
#define BOOST_STACKTRACE_USE_BACKTRACE  
#include <backtrace.h>  
#include <boost/stacktrace.hpp>  
#include <iostream>  
  
int main()  
{  
    boost::stacktrace::stacktrace trace;  
    for (unsigned i = 0; i < trace.size(); ++i) {  
        std::cout << '#' << i << ' ' << trace[i].name() << " (" << trace[i].address() << ") at " << trace[i].source_file() << ':' << trace[i].source_line() << '\n';  
    }  
    return 0;  
}  
```  
Result: (I have already installed boost headers and libbacktrace at `E:/libraries/xxx` and built libbacktrace)  
```powershell  
PS C:\Desktop\demo> g++ main.cpp -o main.exe -IE:/libraries/boost/include -IE:/libraries/libbacktrace -lbacktrace -std=c++20 -g  
PS C:\Desktop\demo> ./main  
#0 register_frame_ctor (0x7ff7ac312dcc) at E:/libraries/boost/include/boost/stacktrace/stacktrace.hpp:109  
#1 register_frame_ctor (0x7ff7ac3014a2) at C:/Desktop/demo/main.cpp:8  
#2 register_frame_ctor (0x7ff7ac3012ef) at :0  
#3 register_frame_ctor (0x7ff7ac301406) at :0  
#4 register_frame_ctor (0x7ffdada7e8d7) at :0  
#5 register_frame_ctor (0x7ffdae90c53c) at :0  
```  
  
## Expected Behavior  
`trace[i].name() ` should return correct function names (such as `main`).  
  
## Environment  
- OS: Windows 11 24H2  
- Compiler: MinGW-w64 GCC 14.2.0 x86_64 posix-seh-msvcrt  
- Boost: 1.88.0  
  
## Investigation  
I found the possible reason using debugger: function calling [`backtrace_syminfo`](https://github.com/boostorg/stacktrace/blob/boost-1.89.0/include/boost/stacktrace/detail/libbacktrace_impls.hpp#L182). For some reason, this function doesn't work.  
  
I change the code in `include/boost/stacktrace/detail/libbacktrace_impls.hpp` to as following, so it will use debug info and it turns to works on my machine.  
```cpp  
    if (state) { // line 173  
        ::backtrace_pcinfo(  
            state,  
            reinterpret_cast<uintptr_t>(addr),  
            boost::stacktrace::detail::libbacktrace_full_callback,  
            boost::stacktrace::detail::libbacktrace_error_callback,  
            &data  
        );  
        // removed backtrace_syminfo  
    }  
```

---

## Comment 1

> Username: prbegd  
> Created at: 2026-01-24 11:09:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/212#issuecomment-3794478058  

After some debugging, I've made progress and I seem to find the reason why  `backtrace_syminfo` doesn't work. (The following is based on mingw-w64+libbacktrace environment, PE/COFF file format)  
  
## Cause  
  
The argument `addr` of `backtrace_syminfo` should be a Virtual Address (VA) based on the *Image Base stored in the executable file*. However, when Address Space Layout Randomization (ASLR) is enabled, the Image Base changes dynamically at runtime, which makes the VA from `frame::address()` based on the dynamic Image Base doesn't match the rule of argument `addr`, so `register_frame_ctor` is returned.  
  
## Possible Solution  
  
1. Get Relative Virtual Address (RVA) by subtracting the dynamic Image Base from VA (from `frame::address()`).  
2. Get correct VA by adding the Image Base stored in the executable file and the RVA.  
3. Pass the correct VA to `backtrace_syminfo`.  
  
I wrote some test code and I confirmed that this solution works on my machine.  
  
## Other Logic Problem  
  
https://stackoverflow.com/a/54351109/ mentioned this problem:  
  
<details>  
<summary>Quote</summary>  
  
> `backtrace_pcinfo` has to be called with two callbacks. The first will receive the resolved symbol info (if any) and the second is an error callback. When a symbol is found the first callback is called and its return value is returned from `backtrace_pcinfo` or in code:  
>  
> return of `backtrace_pcinfo`: `return state->fileline_fn (state, pc, callback, error_callback, data);`  
>  
> `fileline_fn` for mingw is the dwarf implementation `dwarf_fileline` which returns like this:  
>  
> ```lisp  
> ret = dwarf_lookup_pc (state, ddata, pc, callback, error_callback,  
>         data, &found);  
> if (ret != 0 || found)  
>   return ret;  
> dwarf_lookup_pc` now returns what the callback returned when a symbol is found: `return callback (data, pc, filename, lineno, function->name);  
> ```  
>  
> Now lets see how the callback looks boost is supplying:  
>  
> ```cpp  
> inline int libbacktrace_full_callback(void *data, uintptr_t /*pc*/, const char *filename, int lineno, const char *function) {  
>     pc_data& d = *static_cast<pc_data*>(data);  
>     if (d.filename && filename) {  
>         *d.filename = filename;  
>     }  
>     if (d.function && function) {  
>         *d.function = function;  
>     }  
>     d.line = lineno;  
>     return 0;  
> }  
> ```  
>  
> Since the return value is directly propagated to `backtrace_pcinfo` this means that this check will always go into the `or` case no matter if anything was found:  
>  
> ```ruby  
>         ::backtrace_pcinfo(  
>             state,  
>             reinterpret_cast<uintptr_t>(addr),  
>             boost::stacktrace::detail::libbacktrace_full_callback,  
>             boost::stacktrace::detail::libbacktrace_error_callback,  
>             &data  
>         )   
>         ||  
>         ::backtrace_syminfo(  
>             state,  
>             reinterpret_cast<uintptr_t>(addr),  
>             boost::stacktrace::detail::libbacktrace_syminfo_callback,  
>             boost::stacktrace::detail::libbacktrace_error_callback,  
>             &data  
>         );  
> ```  
>  
> This means it will always use the implementation that somehow strips the leading space. I changed the callback to return `1` and now everything is fine.  
</details>  
  
---  
  
Please tell me your opinions. I'm pleased to prepare a PR if this solution is fine.
