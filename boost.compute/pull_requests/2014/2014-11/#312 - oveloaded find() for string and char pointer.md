# #312 oveloaded find() for string and char pointer [Merged]

> Username: Mageswaran1989  
> Created at: 2014-11-25 16:59:53 UTC  
> Updated at: 2014-11-27 06:23:02 UTC  
> Merged at: 2014-11-27 06:23:02 UTC  
> Closed at: 2014-11-27 06:23:02 UTC  
> Url: https://github.com/boostorg/compute/pull/312  

Added support for searching string in bc::string find().  
  
Tried adding doctest.  
  
Please review.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-11-25 17:21:45 UTC  
> Url: https://github.com/boostorg/compute/pull/312#issuecomment-64436832  

[![Coverage Status](https://coveralls.io/builds/1525682/badge)](https://coveralls.io/builds/1525682)  
  
Coverage decreased (-0.0%) when pulling **bcad4017569d218ae82a22ed4973ac4b2213ea50 on Mageswaran1989:string_find_string** into **1e0505d79cb3340d4faaaa79865eefa391c78800 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-11-26 03:38:58 UTC  
> Updated_at: 2014-11-27 06:02:08 UTC  
> Url: https://github.com/boostorg/compute/pull/312#discussion_r20914908  

This doxygen block should be up above the method with the rest of the documentation. See the documentation of the `iota()` algorithm for an example.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-11-26 03:42:45 UTC  
> Updated_at: 2014-11-27 06:02:08 UTC  
> Url: https://github.com/boostorg/compute/pull/312#discussion_r20914995  

So each doctest name needs to be unique (i.e. only one block of coded surrounded by `//! [string_find]`). For the `find()` method I'd have a block which created the input string, created the pattern string, and then called `input.find(pattern)`. In the method documentation in basic_string.hpp I would then describe what the pattern string was and what the `find()` method would return for the example.

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-11-27 05:35:35 UTC  
> Url: https://github.com/boostorg/compute/pull/312#issuecomment-64748184  

[![Coverage Status](https://coveralls.io/builds/1534059/badge)](https://coveralls.io/builds/1534059)  
  
Coverage remained the same when pulling **e16358a81d879637030d86b4727933b8b9fa58f0 on Mageswaran1989:string_find_string** into **1c80c4fd32a62953a3d99ab72828848756dcca9c on kylelutz:develop**.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-11-27 05:52:33 UTC  
> Updated_at: 2014-11-27 06:02:08 UTC  
> Url: https://github.com/boostorg/compute/pull/312#discussion_r20980183  

One small fix, should be `test/test_string.cpp`

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-11-27 05:52:58 UTC  
> Updated_at: 2014-11-27 06:02:08 UTC  
> Url: https://github.com/boostorg/compute/pull/312#discussion_r20980190  

`pattern` is unused here? should there be a test for it?

---

## Comment 7

> Username: coveralls  
> Created_at: 2014-11-27 06:08:14 UTC  
> Url: https://github.com/boostorg/compute/pull/312#issuecomment-64749811  

[![Coverage Status](https://coveralls.io/builds/1534135/badge)](https://coveralls.io/builds/1534135)  
  
Coverage remained the same when pulling **9d813a2309854d805258598fde888cb25f0e483b on Mageswaran1989:string_find_string** into **1c80c4fd32a62953a3d99ab72828848756dcca9c on kylelutz:develop**.

---

## Comment 8

> Username: coveralls  
> Created_at: 2014-11-27 06:17:10 UTC  
> Url: https://github.com/boostorg/compute/pull/312#issuecomment-64750272  

[![Coverage Status](https://coveralls.io/builds/1534130/badge)](https://coveralls.io/builds/1534130)  
  
Coverage remained the same when pulling **9d813a2309854d805258598fde888cb25f0e483b on Mageswaran1989:string_find_string** into **1c80c4fd32a62953a3d99ab72828848756dcca9c on kylelutz:develop**.

---
