# #29 - Compilation error due to a missing file. [Closed]

> Username: vlichevsky  
> Created at: 2017-08-23 19:56:09 UTC  
> Updated at: 2017-09-06 05:42:14 UTC  
> Closed at: 2017-09-01 18:48:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/29  

I am trying to compile a program that uses Boost.Stacktrace and get the following error:  
  
    % g++ -g -I/tmp/boost/include -L/tmp/boost/lib -DBOOST_STACKTRACE_LINK -DBOOST_STACKTRACE_USE_BACKTRACE -lboost_stacktrace_backtrace -ldl -lbacktrace stacktrace-test.cpp -o stacktrace-test                  
    In file included from /tmp/boost/include/boost/stacktrace/frame.hpp:20:0,  
                     from /tmp/boost/include/boost/stacktrace.hpp:15,  
                     from stacktrace-test.cpp:2:  
    /tmp/boost/include/boost/stacktrace/safe_dump_to.hpp:15:10: fatal error: boost/stacktrace/detail/push_options.pp: No such file or directory  
     #include <boost/stacktrace/detail/push_options.pp>  
              ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
    compilation terminated.  
  
I am using Stacktrace library version that is included in Boost 1.65.0 release. Boost was built whith the following commands:  
`./bootstrap.sh`  
`./b2 --prefix=/tmp/boost --with-stacktrace install`  
Resulting installation indeed misses files `boost/stacktrace/detail/push_options.pp` and `boost/stacktrace/detail/pop_options.pp`. Is this a bug, or am I doing something wrong?

---

## Comment 1

> Username: as-xjc  
> Created at: 2017-08-24 15:35:08 UTC  
> Updated at: 2017-08-24 15:35:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/29#issuecomment-324672266  

me too in macos. Finally, I copied the two files from src to include.

---

## Comment 2

> Username: PavlovMU  
> Created at: 2017-08-25 15:00:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/29#issuecomment-324946280  

https://svn.boost.org/trac10/ticket/13178

---

## Comment 3

> Username: vlichevsky  
> Created at: 2017-09-01 18:48:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/29#issuecomment-326656859  

Confirming that this issue is fixed in Boost 1.65.1rc1.

---

## Comment 4

> Username: apolukhin  
> Created at: 2017-09-06 05:42:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/29#issuecomment-327380633  

Thanks for reporting the issue and testing the fix!
