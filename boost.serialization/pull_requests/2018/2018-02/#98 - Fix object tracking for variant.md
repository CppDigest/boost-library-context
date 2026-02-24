# #98 Fix object tracking for variant [Closed]

> Username: ricardocosme  
> Created at: 2018-02-25 00:26:20 UTC  
> Updated at: 2018-05-17 20:30:49 UTC  
> Closed at: 2018-05-17 20:30:49 UTC  
> Url: https://github.com/boostorg/serialization/pull/98  

Fix object tracking for a pointer to an object contained into a  
variant that is an element of a container which loads the `value_type`  
into a local object.  
  
This commit supports:  
- map  
- multimap  
- unordered_map  
- unordered_multimap  
- set  
- multiset  
- unordered_set  
- unordered_multiset  
  
Bugfix proposal for [#13456](https://svn.boost.org/trac10/ticket/13456)  
  
A demo that illustrates the problem:  
Online with C++11, gcc 7.2.0 and Boost 1.67.0: https://wandbox.org/permlink/7S016sfxGxHIQoJT  
  
Package  
[bug_serialization_variant_v2.tar.gz](https://github.com/boostorg/serialization/files/1964907/bug_serialization_variant_v2.tar.gz)  
Build: `b2 -sBOOST_ROOT=<root_directory_of_boost>`  
Run: `stage/demo`

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-02-25 06:00:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/98#issuecomment-368286030  

I would be curious to see a test program which illustrates the original problem

---

## Comment 2

> Username: ricardocosme  
> Created_at: 2018-02-25 11:00:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/98#issuecomment-368300163  

Hi Robert(@robertramey),  
  
Take a look at this demo[1] using C++11. The problem can be reproduced with the latest version of Boost(1.66.0) or the `develop` branch. There is a `Jamroot` and you can build with   
`b2 -sBOOST_ROOT=<root_directory_of_boost>` and execute with `stage/demo`.  
  
Thank you  
  
[1][bug_serialization_variant.tar.gz](https://github.com/boostorg/serialization/files/1755898/bug_serialization_variant.tar.gz)

---

## Comment 3

> Username: robertramey  
> Created_at: 2018-05-01 16:33:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/98#issuecomment-385717282  

I've compiled and tested your demo.cpp file.  It builds without error.  It also runs without error - the assert doesn't trap and the debugger shows ptr->i == 5 which seems to be the intention.

---

## Comment 4

> Username: ricardocosme  
> Created_at: 2018-05-01 19:28:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/98#issuecomment-385764990  

Hi @robertramey,  
  
Hm, ok, let's try again.  
It seems to me that the same address was used. It's possible because the serialized objects contained at the first block were destructed and then at the second block I created new objects. It's feasible to me that your machine constructed the second `Foo` object at the same address used by the first, because it's a free space now. If this happened then it wouldn't be necessary to "reset the address" of the pointer to Foo that was deserialized. This scenario doesn't reproduce the bug. Does it make sense to you?  
  
I forced now that the serialized objects must be live until the end of the demo and I check the address of the pointer to `Foo` instead of the value of `Foo::i`.  
  
Please, take a look at this modified demo and see the error happening:  
https://wandbox.org/permlink/7S016sfxGxHIQoJT  
(C++11, gcc 7.2.0, Boost 1.67.0)  
  
Would it be possible to check the new version[1] of the demo at your environment?  
  
[1][bug_serialization_variant_v2.tar.gz](https://github.com/boostorg/serialization/files/1964907/bug_serialization_variant_v2.tar.gz)

---

## Comment 5

> Username: robertramey  
> Created_at: 2018-05-01 20:55:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/98#issuecomment-385787331  

OK - the second example illustrates the problem.  It shows up in a duplicated object which is equal to the original one.  So one has to check that the addresses of the two objects match - not the that the objects themselves match.  
  
I've also added some of your code to the official test_variant.cpp so that should we ever fix this, it will stay fixed.   It illustrates the failure as well.  So ....  
  
Now on the difficult part.

---

## Comment 6

> Username: ricardocosme  
> Created_at: 2018-05-01 21:42:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/98#issuecomment-385799699  

Yes, my PR added tests at `test/test_reset_object_address.cpp`. We have a better test now. I will update the PR.

---

## Comment 7

> Username: ricardocosme  
> Created_at: 2018-05-01 22:33:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/98#issuecomment-385810435  

PR has been updated with a better test at 081097b.  
  
I am waiting for the Travis and Appveyor to check the tests.  
  
Keep me on touch if you have any questions about my solution.  
  
In a simple way, we can not only `reset_object_address` of the variant without to reset the address of the object contained at the variant. The variant's storage is a subobject of the variant.

---
