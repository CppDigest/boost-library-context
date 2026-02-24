# #16 - Lambdas don't work in macros pre-c++20 [Closed]

> Username: mikezackles  
> Created at: 2020-12-08 19:43:36 UTC  
> Updated at: 2020-12-09 22:53:49 UTC  
> Closed at: 2020-12-08 22:38:00 UTC  
> Url: https://github.com/boostorg/leaf/issues/16  

Up until c++20, the uses of `decltype` in the static asserts for `BOOST_LEAF_ASSIGN` and `BOOST_LEAF_CHECK` prevent patterns like `BOOST_LEAF_AUTO(foo, create([&] { ... }()))` (lambda expression in an unevaluated context). I thought this might be a concern since this library targets c++11. Maybe unchecked versions would make sense?  
  
Anyway, thanks for a useful tool!

---

## Comment 1

> Username: zajo  
> Created at: 2020-12-08 21:03:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/16#issuecomment-741028636  

I'd like to get some more feedback that this is a problem before removing the `static_assert`, IMO the better diagnostics outweighs this potential problem. Do you mind giving me a bit more context, what lead you to hit this problem?

---

## Comment 2

> Username: mikezackles  
> Created at: 2020-12-08 21:37:47 UTC  
> Url: https://github.com/boostorg/leaf/issues/16#issuecomment-741073633  

I totally understand not wanting to remove the `static_assert`, and I don't think you should (unless there's some equivalent alternative). My thought was to add some additional unchecked macros for this situation, but I can also understand not wanting to complicate things. Something along the lines of `BOOST_LEAF_ASSIGN_UNCHECKED` etc.  
  
I'm experimenting with porting some code over from Outcome that uses lambdas fairly liberally, and creating named variables for each is a good bit of extra work/clutter. If I understand correctly this is an issue with passing lambdas inline to any function that might fail, which seems like a reasonably common pattern.  
  
For me it's easy enough to define my own macros until I can move to c++20, but it seemed like something that might be nice to have documented in the issues for other people that run into it. No big deal if there's no fix on your end.

---

## Comment 3

> Username: zajo  
> Created at: 2020-12-08 22:38:00 UTC  
> Url: https://github.com/boostorg/leaf/issues/16#issuecomment-741151347  

I got convinced that this is a problem. It is now fixed on `develop`, I also added a unit test. See https://github.com/boostorg/leaf/commit/e9eb0323c5c00a358bbda4cb453c95bae68657d5. Thanks!

---

## Comment 4

> Username: mikezackles  
> Created at: 2020-12-08 22:52:28 UTC  
> Url: https://github.com/boostorg/leaf/issues/16#issuecomment-741171361  

Awesome! Thank you!

---

## Comment 5

> Username: zajo  
> Created at: 2020-12-09 22:53:03 UTC  
> Updated at: 2020-12-09 22:53:49 UTC  
> Url: https://github.com/boostorg/leaf/issues/16#issuecomment-742114076  

Figured out a way to put back the `static_assert` without breaking use with lambdas, see https://github.com/boostorg/leaf/commit/4a11277356c663f6035acee86ac79e873e9fe0e1.
