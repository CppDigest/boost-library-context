# #64 Add missing include file in test_slist_ptrs [Closed]

> Username: masterleinad  
> Created at: 2017-09-20 17:59:14 UTC  
> Updated at: 2017-10-05 10:30:27 UTC  
> Closed at: 2017-10-05 10:28:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/64  

The test tries to serialize a list so `serialization/list.hpp` should be included.

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-10-04 17:50:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/64#issuecomment-334236774  

questions about this.  
  
This should show up as a test failure.  But on my local system this test isn't being run.  Turns out that I have  
   [ test-bsl-run_files test_slist : A : : [ requires slist ] ] # BOOST_HAS_SLIST ]  
   [ test-bsl-run_files test_slist_ptrs : A : : [ requires slist ] ] # BOOST_HAS_SLIST ] ]  
In my jam file - and something analogous in my CMake files.  So my first question is what platform are you testing on?  
  
I do see this being tested on the boost test matrix at http://www.boost.org/development/tests/develop/developer/serialization.html  
and the tests are passing.  So how did you notice this failure?  
  
Examining the contents of the file test_slist_ptrs.cpp serialization/list.hpp doesn't suggest a problem.  Are you using the most recent version of boost?  What version have you been using for this?  
  
Robert Ramey

---

## Comment 2

> Username: masterleinad  
> Created_at: 2017-10-05 10:28:16 UTC  
> Updated_at: 2017-10-05 10:30:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/64#issuecomment-334425375  

This was while testing with `clang-5`, but I can't reprodue anymore (with a recent version).  
  
Looking more closely, I noticed this while testing with `boost-1.65.0` which seems to use 6b33d1c for that file. Apparently, the issue was already fixed in #57.

---
