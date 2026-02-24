# #80 Fix leak in tls [Merged]

> Username: blastrock  
> Created at: 2016-04-16 09:00:21 UTC  
> Updated at: 2016-04-17 22:12:58 UTC  
> Merged at: 2016-04-17 21:59:29 UTC  
> Closed at: 2016-04-17 21:59:29 UTC  
> Url: https://github.com/boostorg/thread/pull/80  

Hi,  
  
I am experimenting memory leaks with this simple program:  
  
``` cpp  
#include <boost/thread.hpp>  
  
boost::thread_specific_ptr<int> p;  
  
int main(int argc, char *argv[])  
{  
  p.reset(new int);  
  return 0;  
}  
```  
  
I compiled boost with gcc 5.3 and the program with clang 3.8, and I linked with boost 1.60 statically. I ran the program through valgrind (clang sanitizer doesn't see all the leaks, I think this is because I didn't compile boost with the sanitizer).  
  
If the code in BOOST_THREAD_PATCH is commented out, I have 6 allocations leaking. If it is enabled, I have only 2. Then with my patch, all boost thread allocations are freed.  
  
May I know why this BOOST_THREAD_PATCH thing exists, and why has it been removed and then restored?  
  
EDIT: I forgot to say that I didn't test this patch on git boost, only on boost 1.60.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-04-17 21:57:25 UTC  
> Url: https://github.com/boostorg/thread/pull/80#issuecomment-211121416  

Hi,   
  
I rolled back the change https://svn.boost.org/trac/boost/ticket/3926 because it introduced a regression in https://svn.boost.org/trac/boost/ticket/12049.

---

## Comment 2

> Username: viboes  
> Created_at: 2016-04-17 22:12:57 UTC  
> Url: https://github.com/boostorg/thread/pull/80#issuecomment-211122918  

Your patch doesn't works on MacOS when I define BOOST_THREAD_PATCH and i run the regression tests.  
  
```  
...found 14399 targets...  
...updating 2162 targets...  
clang-darwin.compile.c++ ../../../bin.v2/libs/thread/build/clang-darwin-3.7.0_14/debug/threading-multi/pthread/thread.o  
clang-darwin.link.dll ../../../bin.v2/libs/thread/build/clang-darwin-3.7.0_14/debug/threading-multi/libboost_thread.dylib  
clang-darwin.link ../../../bin.v2/libs/thread/test/test_condition_notify_all.test/clang-darwin-3.7.0_14/debug/threading-multi/test_condition_notify_all  
testing.capture-output ../../../bin.v2/libs/thread/test/test_condition_notify_all.test/clang-darwin-3.7.0_14/debug/threading-multi/test_condition_notify_all.run  
/bin/sh: line 9: 24809 Segmentation fault: 11  "../../../bin.v2/libs/thread/test/test_condition_notify_all.test/clang-darwin-3.7.0_14/debug/threading-multi/test_condition_notify_all" > "../../../bin.v2/libs/thread/test/test_condition_notify_all.test/clang-darwin-3.7.0_14/debug/threading-multi/test_condition_notify_all.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
Running 1 test case...  
  
*** No errors detected  
  
EXIT STATUS: 139  
====== END OUTPUT ======  
```

---
