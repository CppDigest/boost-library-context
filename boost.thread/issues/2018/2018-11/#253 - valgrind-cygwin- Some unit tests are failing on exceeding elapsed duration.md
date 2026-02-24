# #253 - valgrind|cygwin: Some unit tests are failing on exceeding elapsed duration [Open]

> Username: jeking3  
> Created at: 2018-11-20 20:45:26 UTC  
> Updated at: 2019-05-10 00:12:05 UTC  
> Url: https://github.com/boostorg/thread/issues/253  

A number of unit tests are failing in various situations because they place an upper bound on elapsed time.  There are problems with this logic:  
  
1. In all cases where duration is specified, for example "this_thread::sleep" or "try_lock_for", the specified duration is a "guaranteed minimum".  The operation will wait "at least" that long.  No guarantees are made that it will not wait longer.  
  
2. The issue is exacerbated by running a more stressful test such as within valgrind or by running tests on an overloaded Appveyor build slave.  
  
Here are some examples of this in action:  
  
On a cygwin (32-bit) job:  
https://ci.appveyor.com/project/jeking3/thread/builds/20434396/job/nrsami3xyqhhp56v#L10016  
```  
====== BEGIN OUTPUT ======  
libs\thread\test\sync\mutual_exclusion\shared_mutex\try_lock_for_pass.cpp(53): test '(d).count() < (ns(max_diff)).count()' ('101460100' < '75000000') failed in function 'void f2()'  
1 error detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
```  
  
On a valgrind job:  
https://travis-ci.org/jeking3/thread/jobs/457490787#L4453  
```  
testing.capture-output ../../bin.v2/libs/thread/test/recursive_mutex__try_lock_p.test/clang-linux-6.0/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/recursive_mutex__try_lock_p.run  
====== BEGIN OUTPUT ======  
==26698== Memcheck, a memory error detector  
==26698== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==26698== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==26698== Command: ../../bin.v2/libs/thread/test/recursive_mutex__try_lock_p.test/clang-linux-6.0/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/recursive_mutex__try_lock_p  
==26698==   
../../libs/thread/test/sync/mutual_exclusion/recursive_mutex/try_lock_pass.cpp(89): test '(d_ms).count() < (max_diff).count()' ('91' < '75') failed in function 'int main()'  
../../libs/thread/test/sync/mutual_exclusion/recursive_mutex/try_lock_pass.cpp(90): test '(d_ns).count() < (ns(max_diff)).count()' ('91304790' < '75000000') failed in function 'int main()'  
2 errors detected.  
```  
  
Other jobs have shown up to 175ms unexpected delay.  I am planning on changing the file (misspelled) "timming.hpp" to use 250 for all cases.  That will clear up most of these issues in CI.

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-21 20:20:26 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-440797315  

Hi, this is not a solution.   
I would prefer to inhibit those test on some circumstances, changing them wouldn't test anything and will postpone the issue.

---

## Comment 2

> Username: viboes  
> Created at: 2018-11-21 21:02:08 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-440807248  

An alternative is to change the timing depending on the platform or environment.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-21 22:37:48 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-440833298  

I restored the "develop" copy of the timming.hpp header and I changed 75 to 125, since the clang-6.0 test I referenced took 90 and the cygwin test I referenced took 105, and left 250 alone.  We'll see how that handles it.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-22 00:10:48 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-440858297  

I changed it to 100 for the baseline (due to the 90 I saw on clang-6), and added cygwin to win32 at 250.

---

## Comment 5

> Username: viboes  
> Created at: 2018-11-22 06:54:10 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-440930764  

I believe that you should enter the VALGRIND macro on the definition of those values, without changing the default. What do you think?

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-22 12:55:33 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-441022434  

Sure, that makes sense.

---

## Comment 7

> Username: jeking3  
> Created at: 2018-11-25 13:02:51 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-441438955  

This issue will be resolved by the CI work.

---

## Comment 8

> Username: viboes  
> Created at: 2019-05-09 19:31:21 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-491035811  

Where we are concerning this issue?

---

## Comment 9

> Username: jeking3  
> Created at: 2019-05-09 20:08:10 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-491047285  

I sync'd with the latest boost-ci and re-running to see where we're at post-1.70.0.

---

## Comment 10

> Username: jeking3  
> Created at: 2019-05-10 00:12:05 UTC  
> Url: https://github.com/boostorg/thread/issues/253#issuecomment-491109837  

Yeah, the unit tests are too much for CI.  We need to start honoring the BOOST_NO_STRESS_TEST setting so  CI has a chance of passing.
