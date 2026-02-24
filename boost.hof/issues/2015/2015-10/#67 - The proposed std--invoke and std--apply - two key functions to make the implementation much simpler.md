# #67 - The proposed std::invoke and std::apply - two key functions to make the implementation much simpler [Closed]

> Username: viboes  
> Created at: 2015-10-04 14:11:53 UTC  
> Updated at: 2016-03-07 00:10:34 UTC  
> Closed at: 2015-12-17 20:33:07 UTC  
> Url: https://github.com/boostorg/hof/issues/67  

The implementation would be much more simpler if we have an `invoke` function that follows the `INVOKE` protocol.  
  
The `std::apply` C++14 function would help also as well but it should be INVOKE compatible.  
  
Finally a `is_callable` trait will complete the picture.  
  
Unfortunately the function in Boost.Thread detail is broken at least for C++98 compilers.  
  
See   
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3915.pdf  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4169.html   
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4170.html  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0077r0.html

---

## Comment 1

> Username: K-ballo  
> Created at: 2015-10-04 14:22:41 UTC  
> Url: https://github.com/boostorg/hof/issues/67#issuecomment-145353918  

> The std::apply C++14 function would help also as well but it shoul dbe INVOKE compatible  
  
It's  `std::experimental::apply` and is in the Library Fundamentals TS. It's now `INVOKE` based thanks to [LWG2418](http://cplusplus.github.io/LWG/lwg-defects.html#2418).

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-10-05 07:07:13 UTC  
> Url: https://github.com/boostorg/hof/issues/67#issuecomment-145446376  

I don't see how it makes the "implementation" simpler.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-12-17 20:33:07 UTC  
> Url: https://github.com/boostorg/hof/issues/67#issuecomment-165574763  

`fit::apply` uses the `INVOKE` operator.
