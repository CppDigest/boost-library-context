# #103 - Request: Adapted std::get and an unpack function [Closed]

> Username: beojan  
> Created at: 2021-02-02 17:40:19 UTC  
> Updated at: 2021-04-01 09:17:39 UTC  
> Closed at: 2021-04-01 09:17:39 UTC  
> Url: https://github.com/boostorg/phoenix/issues/103  

With C++11 and above, it would be nice to have an adapted version of `std::get` (or even better, `get` with argument-dependent lookup so it works on anything structured bindings work on) available as part of Phoenix. For `tuples` this is the same as `at_c`, but it:  
1. Can be called with the type instead of the index of the item  
2. Works with a lot more than just tuples  
3. Doesn't require Fusion or the Fusion adapter machinery  
  
Working with `tuples` could be made even nicer by also having a (higher-order) `unpack` function (like [boost::hof::unpack](https://www.boost.org/doc/libs/master/libs/hof/doc/html/include/boost/hof/unpack.html) or [boost::hana::fuse](https://www.boost.org/doc/libs/1_63_0/libs/hana/doc/html/group__group-Foldable.html#ga19fcf61d8d1179903952c0f564c538aa)) which allows tuple elements to be accessed as `arg1...argN`.

---

## Comment 1

> Username: djowel  
> Created at: 2021-02-03 00:57:53 UTC  
> Url: https://github.com/boostorg/phoenix/issues/103#issuecomment-772123763  

Would you like to propose a PR?

---

## Comment 2

> Username: beojan  
> Created at: 2021-02-03 14:44:03 UTC  
> Url: https://github.com/boostorg/phoenix/issues/103#issuecomment-772561506  

Here you go: https://github.com/boostorg/phoenix/pull/104.
