# #136 - Seeing random serialization failures in unit tests [Closed]

> Username: jeking3  
> Created at: 2018-10-31 14:01:55 UTC  
> Updated at: 2018-11-01 14:34:31 UTC  
> Closed at: 2018-11-01 14:34:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/136  

I can re-run a build job that fails and it disappears.  It's happening around 5% of the time on the jobs I've seen.  This is new behavior as of the last 24 hours or so.  
  
Example:  
https://travis-ci.org/boostorg/ptr_container/jobs/448820164#L1935  
  
I'm going to refresh my CI enhancement pull request for this repo to get more coverage.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-31 15:23:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/136#issuecomment-434729672  

I have been tweaking a couple of tests.  Hopefully this should get resolved in the course of today.  I can't explain why results fluctuate.  We'll take a look when I make the next set of changes.  Hopefully these will be the last for a while

---

## Comment 2

> Username: jeking3  
> Created at: 2018-10-31 22:59:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/136#issuecomment-434876197  

Manifesting in other ways:  
```  
testing.capture-output ../../bin.v2/libs/ptr_container/test/serialization.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/serialization.run  
====== BEGIN OUTPUT ======  
==9929== Memcheck, a memory error detector  
==9929== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==9929== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==9929== Command: ../../bin.v2/libs/ptr_container/test/serialization.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/serialization  
==9929==   
Running 1 test case...  
test/serialization.cpp(254): error: in "Pointer Container Test Suite/test_serialization": check p->i == d->i has failed [42 != 2]  
test/serialization.cpp(256): error: in "Pointer Container Test Suite/test_serialization": check dynamic_cast<Derived*>( d ) has failed  
*** 2 failures are detected in the test module "Master Test Suite"  
```

---

## Comment 3

> Username: robertramey  
> Created at: 2018-11-01 04:27:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/136#issuecomment-434926993  

hmm - I can't speak to valgrind.

---

## Comment 4

> Username: robertramey  
> Created at: 2018-11-01 04:31:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/136#issuecomment-434927424  

or to ptr_container for that matter

---

## Comment 5

> Username: robertramey  
> Created at: 2018-11-01 04:32:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/136#issuecomment-434927580  

I don't think the master branch has changed other than the setting of visibility=hidden as the default.  And THAT could definitely effect the results for any serialization test with the word "ptr" in it.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-01 12:38:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/136#issuecomment-435027976  

Let's see what the result is of the expanded CI suite I have running right now.
