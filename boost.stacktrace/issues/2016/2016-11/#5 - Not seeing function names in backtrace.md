# #5 - Not seeing function names in backtrace [Closed]

> Username: eyalroz  
> Created at: 2016-11-21 09:46:58 UTC  
> Updated at: 2016-12-10 09:50:57 UTC  
> Closed at: 2016-12-10 09:50:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/5  

I just downloaded a zip of the master branch, unzipped and built with:  
````  
g++ --std=c++14 -o throwing throwing_st.cpp -g -I ../include/ -ldl  
````  
on a system with Boost 1.60 installed. Running the resulting binary, I get:  
```  
'i' must not be greater than zero in oops()  
Backtrace:  
 0# ?? at 0x00401C70  
 1# ?? at 0x00401FF7  
 2# ?? at 0x00402148  
 3# ?? at 0x004014E6  
 4# ?? at 0x0040159D  
 5# ?? at 0x004015B8  
 6# ?? at 0x00401591  
 7# ?? at 0x004015B8  
 8# ?? at 0x00401591  
 9# ?? at 0x004015B8  
10# ?? at 0x00401591  
11# ?? at 0x004015B8  
12# ?? at 0x00401591  
13# ?? at 0x004015B8  
14# ?? at 0x00401591  
15# ?? at 0x004015B8  
16# ?? at 0x004015CD  
17# __libc_start_main  
18# ?? at 0x004013B9  
```  
Why don't I see at least function names in the trace?

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-11-22 07:04:31 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/5#issuecomment-262163724  

You're using the default "fallback" backend that is very limited in functionality but always available http://apolukhin.github.io/stacktrace/boost_stacktrace/build_macros_and_backends.html  
  
Define BOOST_STACKTRACE_USE_LIBUNWIND and link with libunwind. This will make all the functions names visible.  
  
Meanwhile, I'll try to make the default fallback backend more usable.

---

## Comment 2

> Username: eyalroz  
> Created at: 2016-11-22 10:57:50 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/5#issuecomment-262210443  

Perhaps you should consider a warning when the default fallback backend is used (which can be made to go away with a define for people who have settled for the default backend).

---

## Comment 3

> Username: apolukhin  
> Created at: 2016-12-10 09:50:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/5#issuecomment-266194234  

Improved function name detection, now uses DWARF debug info fir getting function name too.
