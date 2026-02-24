# #171 - First frame of stacktrace wrong [Closed]

> Username: NessajHu  
> Created at: 2024-07-15 04:26:12 UTC  
> Updated at: 2024-08-08 01:35:01 UTC  
> Closed at: 2024-08-08 01:35:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/171  

## Description  
I write a simple program as below  
```cpp  
#include <iostream>  
#define BOOST_STACKTRACE_USE_ADDR2LINE  
#include <boost/stacktrace.hpp>  
  
int main() {  
    std::cout << boost::stacktrace::stacktrace();  
}  
```  
the output is:  
```text  
 0# boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::basic_stacktrace() at <MyBoostInstallDir>/include/boost/stacktrace/stacktrace.hpp:127  
 1# __libc_start_main in /lib64/libc.so.6  
 2# _start in <MyApplication>  
```  
When it runs on [[https://godbolt.org/z/4vKh4j4Mr](https://godbolt.org/z/4vKh4j4Mr)], output is  
```text  
 0# main in ./output.s  
 1# 0x00007B4BA2229D90 in /lib/x86_64-linux-gnu/libc.so.6  
 2# __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6  
 3# _start in ./output.s  
```  
It seems like constructor of stacktrace replace main function  
  
## System  
OS: Centos 7  
Boost Version: 1.72.0  
Compiler: gcc 9.5.0  
  
Any Suggestions?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-07-15 08:19:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/171#issuecomment-2227940642  

Looks like the `main() ` function was inlined. Could you reproduce the issue on godbolt?

---

## Comment 2

> Username: NessajHu  
> Created at: 2024-07-15 08:26:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/171#issuecomment-2227953425  

> Looks like the `main() ` function was inlined. Could you reproduce the issue on godbolt?  
  
@apolukhin   
I'm afraid not. The situation was not initially discovered in this use case. It was actually in an example involving a virtual function, where the type was passed in from the command line. In such cases, inlining is not possible. Moreover, under normal circumstances, there should be no call to the stacktrace constructor

---

## Comment 3

> Username: NessajHu  
> Created at: 2024-07-16 02:33:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/171#issuecomment-2229886377  

I have conducted some tests and found that the issue occurs when using the -g flag with GCC 9.5.0. My setup with GCC 4.8.5 does not exhibit this problem.  
  
I would appreciate it if someone could look into this matter. Thank you for your attention and support.

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-07-16 05:40:55 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/171#issuecomment-2230067774  

There's no way I could look into this matter, if there's no reproducer for it. Provide a link to godbolt that reproduces the problem

---

## Comment 5

> Username: jplcz  
> Created at: 2024-08-06 06:19:00 UTC  
> Updated at: 2024-08-06 06:22:38 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/171#issuecomment-2270471453  

Stack traces are generallty not always reliable, unless you're running in debugger with full symbol information. Additionally stack trace may "break" (can't continue after some point) if unwinder hits try/catch block, as DWARF/ARM EH decoder will call custom unwind routine, which performs further execution path changes - this is something that "passive" unwind can't handle. Symbols may be "missing" if linker hides them from dynamic symbol table. If you want somewhat more "reliable" symbolic information you can consider ``-Wl,--export-dynamic`` (ELF based OSes) as executable link flags, or split app code into .so and stub main file, which calls into shared object's "real" main.
