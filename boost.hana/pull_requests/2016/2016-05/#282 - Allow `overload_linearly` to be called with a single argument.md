# #282 [overload_linearly] Allow `overload_linearly` to be called with a single argument [Closed]

> Username: vittorioromeo  
> Created at: 2016-05-27 19:31:32 UTC  
> Updated at: 2016-05-29 17:09:51 UTC  
> Closed at: 2016-05-29 17:09:51 UTC  
> Url: https://github.com/boostorg/hana/pull/282  

Fixes #280.  
  
> [overload_linearly] Allow `overload_linearly` to be called with a single argument  
> - Added an `make_overload_linearly_t::operator()` overload that takes a single argument, and simply > returns the argument  
  
_(I closed the previous pull request because I completely forgot to create a new branch.)_

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-05-28 01:24:27 UTC  
> Updated_at: 2016-05-29 07:48:53 UTC  
> Url: https://github.com/boostorg/hana/pull/282#discussion_r64981674  

If you call `overload_linearly(some-expr)` where `some-expr` is a temporary, which should often be the case because we'll use it with a lambda like `overload_linearly([](...) {...})`, you'll end up returning an rvalue reference to the temporary. Instead, and to mimic the behaviour of `overload`, you should use `hana::detail::decay` (equivalent to `std::decay`, but faster) to make sure a copy of the function is made.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-05-28 01:25:46 UTC  
> Updated_at: 2016-05-29 07:48:53 UTC  
> Url: https://github.com/boostorg/hana/pull/282#discussion_r64981704  

What I mean is that instead of `decltype(auto)`, it should be `typename detail::decay<F>::type` according to my argument above (which you could argue against though).

---

## Comment 3

> Username: vittorioromeo  
> Created_at: 2016-05-28 10:54:22 UTC  
> Url: https://github.com/boostorg/hana/pull/282#issuecomment-222302570  

I agree with your argument, @ldionne: the updated commit (8e0c397ea9988d834a0f85f1da732bdc7124668d) now returns `typename detail::decay<F>::type`.  
  
Two unrelated nitpicks:  
- There are many instances in the codebase where `std::decay` is used instead of `detail::decay`. Is there any reason for that?   
- There also are many instances where `typename detail::decay<...>::type` is used - is there any reason for not defining a `decay_t` alias to avoid the `typename [...] ::type` boilerplate?  
  
I could work on fixing both points if they're oversights.

---

## Comment 4

> Username: ldionne  
> Created_at: 2016-05-28 15:37:16 UTC  
> Url: https://github.com/boostorg/hana/pull/282#issuecomment-222314577  

Thanks, that looks perfect. Regarding the nitpicks:  
  
> There are many instances in the codebase where std::decay is used instead of detail::decay. Is there any reason for that?  
  
Oversight and laziness. But `detail::decay` should already be used in most places that are "performance critical". I would welcome a PR using `detail::decay` instead of `std::decay` in implementation details, but don't use `detail::decay` in examples and unit tests; it's just an implementation detail.  
  
> There also are many instances where `typename detail::decay<...>::type` is used - is there any reason for not defining a `decay_t` alias to avoid the `typename [...] ::type` boilerplate?  
  
As an implementation detail for optimization purposes, I'd rather not add an additional indirection through a template alias. It's probably negligible, but the added readability is also negligible IMO (since `detail::decay` is already being used only in a few, narrow places.

---

## Comment 5

> Username: ldionne  
> Created_at: 2016-05-28 23:11:26 UTC  
> Url: https://github.com/boostorg/hana/pull/282#issuecomment-222333929  

Can you please rebase on top of the current develop? That'll allow the Travis build to pass and then I'll merge.

---

## Comment 6

> Username: vittorioromeo  
> Created_at: 2016-05-29 07:50:27 UTC  
> Url: https://github.com/boostorg/hana/pull/282#issuecomment-222347678  

Done.

---

## Comment 7

> Username: ldionne  
> Created_at: 2016-05-29 17:09:51 UTC  
> Url: https://github.com/boostorg/hana/pull/282#issuecomment-222371429  

Closed by 03b948918e0935ee0255ab8c442141b2ec175467...a5989ca1901f79b6a0d315ad764f28b81ec0a4cb

---
