# #97 - Not getting line numbers with addr2line [Closed]

> Username: eyalroz  
> Created at: 2020-07-17 08:49:27 UTC  
> Updated at: 2023-08-27 18:49:51 UTC  
> Closed at: 2023-08-27 18:34:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97  

I'm building this program based on the first example in the documentation:  
```  
#define BOOST_STACKTRACE_USE_ADDR2LINE   
  
#include <boost/stacktrace.hpp>  
#include <iostream>  
  
void bar(int n) {  
    if (n <= 0) {  
        // ... somewhere inside the `bar(int)` function that is called recursively:  
        std::cout << boost::stacktrace::stacktrace();  
        return;  
    }  
    bar(n-1);  
}  
  
int main() {  
    bar(4);  
}  
```  
I'm using Boost 1.73.0, downloaded built and installed on my Devuan GNU/Linux Beowulf (~= Debian Buster but without systemd) machine. I have both libbacktrace and addr2line installed, and the default compiler is g++-8.3.0 . I'm compiling with `-g`.  
  
I get:  
```  
$ ./plain_vanilla   
 0# 0x000055DBD3E2B4BB in ./plain_vanilla  
 1# 0x000055DBD3E2B4E9 in ./plain_vanilla  
 2# 0x000055DBD3E2B4E9 in ./plain_vanilla  
 3# 0x000055DBD3E2B4E9 in ./plain_vanilla  
 4# 0x000055DBD3E2B4E9 in ./plain_vanilla  
 5# 0x000055DBD3E2B51A in ./plain_vanilla  
 6# __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6  
 7# 0x000055DBD3E2B3CA in ./plain_vanilla  
```  
I expect to get the line numbers... shouldn't I get them?

---

## Comment 1

> Username: narodnik  
> Created at: 2020-07-21 07:40:09 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97#issuecomment-661691287  

I'm getting the same problem and have tried everything. There should be an addendum in the documentation because the default behaviour I get doesn't match what I see in the docs output (just the binary name, not filename:lineno).  
  
```cpp  
#include <iostream>  
#include <boost/stacktrace.hpp>  
  
int foo(bool xx)  
{  
    std::cout << boost::stacktrace::stacktrace();  
    return 22;  
}  
  
int main()  
{  
    foo(false);  
    return 0;  
}  
```  
  
```bash  
$ g++ -DBOOST_STACKTRACE_USE_ADDR2LINE  -ggdb -rdynamic bb.cpp -o bb -lboost_stacktrace_addr2line -ldl -O0 -fno-omit-frame-pointer -lboost_stacktrace_addr2line && ./bb  
 0# foo(bool) in ./bb  
 1# main in ./bb  
 2# __libc_start_main in /usr/lib/libc.so.6  
 3# _start in ./bb  
```  
```bash  
$ g++  -g -rdynamic bb.cpp -o bb -ldl  && ./bb  
 0# foo(bool) in ./bb  
 1# main in ./bb  
 2# __libc_start_main in /usr/lib/libc.so.6  
 3# _start in ./bb  
```  
  
I tried many different variations of the switches and so on but always the output is the same.

---

## Comment 2

> Username: ivanarh  
> Created at: 2020-07-21 21:13:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97#issuecomment-662109411  

The code that uses addr2line to get a source file and line is broken:  
See https://github.com/boostorg/stacktrace/blob/develop/include/boost/stacktrace/detail/addr2line_impls.hpp#L209  
We pass **addr_** field as an argument to addr2line executable.  
**addr_** is a virtual address of instruction (return address from stack)  
But addr2line can't work with virtual address, it accepts only offset within executable (or offset within section with the option).  
https://linux.die.net/man/1/addr2line  
> addr2line translates addresses into file names and line numbers. Given an address in an executable or an offset in a section of a relocatable object, it uses the debugging information to figure out which file name and line number are associated with it.  
  
To be honest, I'm not sure that this feature has ever worked.

---

## Comment 3

> Username: eyalroz  
> Created at: 2020-07-21 23:40:25 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97#issuecomment-662160781  

@ivanarh :  
  
> To be honest, I'm not sure that this feature has ever worked.  
  
Then...  
1.  how did it make it into the Boost release? :-(  
2. Shouldn't it be removed?

---

## Comment 4

> Username: ivanarh  
> Created at: 2020-07-22 09:33:35 UTC  
> Updated at: 2020-07-22 09:34:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97#issuecomment-662350489  

> how did it make it into the Boost release? :-(  
  
I don't know exactly. But I suspect that it's happened because boost stacktrace uses GNU libbacktrace from gcc to determine line numbers. Addr2line is used as a fallback when libbacktrace is disabled during compilation or getting a line number has failed.  
  
> Shouldn't it be removed?  
  
It's a question for stacktrace maintainer(s). I hope it may be fixed easily, all we need is to perform easy arithmetic with Dl_info structure fields: https://man7.org/linux/man-pages/man3/dladdr.3.html  
It has **dli_fbase** field, all we need is to substract it from **addr_** value before passing to  addr2line.

---

## Comment 5

> Username: Kidsunbo  
> Created at: 2021-09-02 13:25:25 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97#issuecomment-911680648  

I find that on linux, if I only use header-only version. Everything works fine. If linking with library is the only option, do not link with `boost_stacktrace_addr2line` and link with `boost_stacktrace_backtrace`, the line number will show up as well.

---

## Comment 6

> Username: ja2142  
> Created at: 2023-06-04 15:32:52 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97#issuecomment-1575611889  

> To be honest, I'm not sure that this feature has ever worked.  
  
It did (and still does) work for binaries that aren't position independent.  
  
> I find that on linux, if I only use header-only version. Everything works fine.  
  
With `BOOST_STACKTRACE_USE_ADDR2LINE` I get unresolved addresses whether I link to boost_stacktrace_addr2line (with `BOOST_STACKTRACE_LINK` defined) or not.  
  
There are two, somewhat separate problems I see with current boost_stacktrace_addr2line on linux (assuming gcc, but clang seems to work the same in that regard):  
 - As others noticed, the address passed to addr2line is a randomized address, addr2line can't understand it. This can be fixed in a relatively straightforward way: just find the base of searched address in `/proc/self/maps` and subtract it before passing it to addr2line - this offset should be understood by addr2line. Alternatively, this can be worked around with `-no-pie` linker option.  
 - The address resolution to function name relies on `dladdr()`. For binary compiled with `-pie` (which is probably default on modern systems?) `-rdynamic` option is needed to put all symbols in dynamic symbol table, so that `dladdr()` can resolve them (for some reason in `-no-pie` executables everything seems to resolve without `-rdynamic` for me). This isn't easily fixable in boost, without reading the executable and manually parsing debug information stored within (which is probably what backtrace is doing?), but compiling with `-rdynamic` or `-no-pie` is an easy workaround.  
  
Here are options for anyone wanting to just get stacktraces:  
 - Link with boost_stacktrace_backtrace. Stacktraces should work, but this does introduce additional dependency on libbacktrace.  
 - Using addr2line: link with `-rdynamic` option. Stacktraces should have function names, but probably no file info (for now, maybe someone will fix it in the future?). From my understanding `-rdynamic` only slows down loading of an executable, so probably not a big deal for most.   
 - Using addr2line: link with `-no-pie` option. Nice stacktraces, but pie is a useful security hardening measure.  
 - Wait couple of years for standard stacktraces in C++23.

---

## Comment 7

> Username: Fedr  
> Created at: 2023-08-27 18:49:51 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/97#issuecomment-1694735716  

Related discussion: https://stackoverflow.com/q/62973197/7325599
