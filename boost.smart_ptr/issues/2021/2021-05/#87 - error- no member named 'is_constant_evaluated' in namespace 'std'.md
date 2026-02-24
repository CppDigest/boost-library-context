# #87 - error: no member named 'is_constant_evaluated' in namespace 'std' [Closed]

> Username: JeffGarland  
> Created at: 2021-05-02 15:21:30 UTC  
> Updated at: 2021-05-02 15:42:39 UTC  
> Closed at: 2021-05-02 15:42:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/87  

clang 7 and clang 8 in 2a mode -- this is 1.76 release of smart ptr code.  My quick take on this is this is a standard library issue and not smart_ptr as you're not using this at all.  Or maybe it's something with CI setup.  Anyway, giving the heads up for more expert evaluation.  
  
 "clang++" -c -x c++ -std=c++2a -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -I"."  -o "bin.v2/libs/date_time/test/testclocks.test/clang-linux-8/debug/cxxstd-2a-iso/threading-multi/visibility-hidden/local_time/testclocks.o" "libs/date_time/test/local_time/testclocks.cpp"  
  
clang-linux.compile.c++.without-pch bin.v2/libs/date_time/test/testclocks.test/clang-linux-8/debug/cxxstd-2a-iso/threading-multi/visibility-hidden/local_time/testclocks.o  
In file included from libs/date_time/test/local_time/testclocks.cpp:8:  
In file included from ./boost/date_time/local_time/local_time.hpp:11:  
In file included from ./boost/date_time/posix_time/posix_time.hpp:24:  
In file included from ./boost/date_time/posix_time/time_formatters.hpp:16:  
In file included from ./boost/date_time/posix_time/posix_time_types.hpp:10:  
In file included from ./boost/date_time/time_clock.hpp:17:  
In file included from ./boost/shared_ptr.hpp:17:  
In file included from ./boost/smart_ptr/shared_ptr.hpp:17:  
In file included from ./boost/smart_ptr/detail/shared_count.hpp:27:  
In file included from ./boost/smart_ptr/detail/sp_counted_impl.hpp:35:  
/usr/lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/memory:177:16: error: no member named 'is_constant_evaluated' in namespace 'std'  
      if (std::is_constant_evaluated())  
          ~~~~~^  
1 error generated.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-05-02 15:30:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/87#issuecomment-830827153  

Here's clang-8 in 2a mode: https://github.com/boostorg/smart_ptr/runs/2382460616?check_suite_focus=true

---

## Comment 2

> Username: pdimov  
> Created at: 2021-05-02 15:35:40 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/87#issuecomment-830827886  

The differences I'm seeing in the CI setups are (1) you're using `ubuntu-16.04` and I'm using `ubuntu-20.04` in this config, and (2) your error messages imply libstdc++-9, whereas my run is using libstdc++-10.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2021-05-02 15:35:49 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/87#issuecomment-830827905  

hmm, maybe something with the CI setup is off -- and actually what I said about 1.76 isn't correct because the CI is running against develop -- I'm locally testing with 1.76.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-05-02 15:41:11 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/87#issuecomment-830828622  

Not all Clangs work in all modes against all possible libstdc++ versions. libstdc++ version X is only tested against GCC version X.

---

## Comment 5

> Username: JeffGarland  
> Created at: 2021-05-02 15:42:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/87#issuecomment-830828805  

Gotcha -- I'm looking at my CI setup now will look at updating to 20.04 -- that sounds like an easy fix.  Thanks for the assist.
