# #43 Fix ambiguity of std::begin/end [Merged]

> Username: hp-peti  
> Created at: 2021-08-10 15:07:46 UTC  
> Updated at: 2021-10-02 20:36:51 UTC  
> Merged at: 2021-10-02 20:36:46 UTC  
> Closed at: 2021-10-02 20:36:46 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43  

- this patch should restore the range-based for functionality for pull-type coroutines  
fixes #39   
fixes #42

---

## Review 1 [Commented]

> Username: jwakely  
> Created_at: 2021-08-11 09:11:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/coroutine2/pull/43#pullrequestreview-727243009  

📁 include/boost/coroutine2/detail/pull_coroutine.hpp

```diff
 309 |- }
 299 |+ using boost::coroutines2::detail::begin;
 300 |+ using boost::coroutines2::detail::end;
```

> Username: jwakely  
> Created_at: 2021-08-11 09:11:52 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43#discussion_r686647842  

This is still undefined behaviour. You cannot add declarations to namespace `std`.  
  
What are the (undefined) overloads of `std::begin` and `std::end` trying to achieve? To ensure that qualified calls to `std::begin` and `std::end` will use the customized boost ones? Don't do that.  
  
If you want `std::begin` and `std::end` to be valid for a `pull_coroutine` (because it is meant to be container-like) then add `pull_coroutine::begin()` and `pull_coroutine::end()` member functions. If it's not meant to be container-like, then `std::begin(coro)` and `std::end(coro)` should not work with it (since they are constrained to call `coro.begin()` and `coro.end()` respectively).

> Username: jwakely  
> Created_at: 2021-08-11 10:54:46 UTC  
> Updated_at: 2021-08-11 10:58:51 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43#discussion_r686719687  

Alternatively, just remove these undefined declarations completely. Require users to use unqualified `begin(coro)` and `end(coro)` calls, which will find the overloads in `boost::coroutines2::detail` using ADL.  
  
If the aim is to support range-based `for` loops, then you do not need the declarations in `std`. A range-based `for` will use member `c.begin()` and `c.end()` if they exist, otherwise will use ADL to find `begin(c)` and `end(c)`.

> Username: hp-peti  
> Created_at: 2021-08-11 13:43:34 UTC  
> Updated_at: 2021-08-11 14:08:55 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43#discussion_r686844790  

I mostly agree but see https://github.com/boostorg/coroutine2/issues/31  
I think specializing std::begin/std::end for user-defined types is accepted though.  
Also the patch above is backwards compatible with the already released sources (it might not need to be).

> Username: jwakely  
> Created_at: 2021-08-11 14:39:15 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43#discussion_r686897108  

> I mostly agree but see #31  
  
So fix the docs :shrug:   
  
That's why I asked about the intended API above. If the point is just to enable range-based `for` then the undefined overloads are not needed. If being able to call `std::begin(coro)` is essential for some reason (even if that's just because it has been documented as supported) then add `coro.begin()` and `coro.end()` member functions, so that the generic `std::begin` and `std::end` can use them. The docs would be lying (they aren't "overloaded") but calling `std::begin(coro)` would still  work, and do the same thing as before. Just without undefined behaviour.  
  
>  I think specializing std::begin/std::end for user-defined types is accepted though.  
  
Yes, but those function templates are not specializations, they are overloads, which are not allowed. And specializing is a bad idea even if it's allowed. http://www.gotw.ca/publications/mill17.htm  
  
Adding names to `std` via a using-declaration is not allowed though, so the change in this PR is still undefined behaviour.  
  
> Also the patch above is backwards compatible with the already released sources (it might not need to be).  
  
But still undefined behaviour.

> Username: hp-peti  
> Created_at: 2021-08-13 19:32:22 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43#discussion_r688737394  

Thank you for the extensive clarification.  
  
Please note the modified pull request which defines .begin and .end members, eliminating the need for new definitions in the std namespace.


---

## Comment 2

> Username: hp-peti  
> Created_at: 2021-08-16 10:35:56 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43#issuecomment-899406043  

@olk could you take a look at this?

---

## Comment 3

> Username: olk  
> Created_at: 2021-10-02 20:36:51 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/43#issuecomment-932817158  

ty

---
