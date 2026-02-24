# #245 Adding empty(hist, cov) [Merged]

> Username: henryiii  
> Created at: 2019-10-26 03:53:49 UTC  
> Updated at: 2019-10-27 08:42:32 UTC  
> Merged at: 2019-10-27 08:42:24 UTC  
> Closed at: 2019-10-27 08:42:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/245  

This closes #241. I did not select a default for coverage, and I was not able to add one test that I wanted to due to bug #244 (not related to this PR, though).

---

## Review 1 [Commented]

> Username: HDembinski  
> Created_at: 2019-10-26 12:07:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/245#pullrequestreview-307523521  

📁 include/boost/histogram/algorithm/empty.hpp

```diff
   1 |+ // Copyright 2019 Hans Dembinski and Henry Schreiner
```

> Username: HDembinski  
> Created_at: 2019-10-26 12:07:26 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339297758  

Since you wrote the code, I think only you should be listed here. I will add my name if I make changes.

> Username: henryiii  
> Created_at: 2019-10-26 12:08:26 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339297793  

Okay, I didn't know what the right thing to put here was.


---

## Review 2 [Commented]

> Username: HDembinski  
> Created_at: 2019-10-26 12:08:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/245#pullrequestreview-307523536  

📁 include/boost/histogram/algorithm/empty.hpp

```diff
   8 |+ #define BOOST_HISTOGRAM_ALGORITHM_EMPTY_HPP
   9 |+ 
  10 |+ #include <boost/histogram/coverage.hpp>
```

> Username: HDembinski  
> Created_at: 2019-10-26 12:08:05 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339297788  

There is no such header, that's why the tests fail.

> Username: henryiii  
> Created_at: 2019-10-26 12:09:39 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339297830  

Oops, missed that. I moved it to a new header, then realized it wasn't required for this PR since I'm using `indexed`, so left that off the PR. Forget I imported it here. My computer still has the header so it doesn't fail locally.

> Username: HDembinski  
> Created_at: 2019-10-26 12:10:09 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339297841  

Since the coverage enum is used by several algorithms now, it should indeed be moved into another header, but the header should be boost/histogram/algorithm/coverage.hpp

> Username: HDembinski  
> Created_at: 2019-10-26 12:11:19 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339297864  

Actually, no, it should just live in fwd.hpp.

> Username: henryiii  
> Created_at: 2019-10-26 12:47:12 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339298892  

It seems odd to have something live in fwd that doesn't live anywhere else...

> Username: henryiii  
> Created_at: 2019-10-26 13:20:08 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339299886  

Though, since it is always part of a function call, and can't be used on its own, I can see why it could make sense there.


---

## Review 3 [Commented]

> Username: HDembinski  
> Created_at: 2019-10-26 12:18:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/245#pullrequestreview-307523597  

📁 include/boost/histogram/algorithm/empty.hpp

```diff
  25 |+ auto empty(const histogram<A, S>& h, coverage cov) {
  26 |+   using value_type = typename histogram<A, S>::value_type;
  27 |+   value_type zero = value_type();
```

> Username: HDembinski  
> Created_at: 2019-10-26 12:14:57 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339297978  

This can be const and I think it should be "default_value" instead of "zero".

> Username: henryiii  
> Created_at: 2019-10-26 12:47:20 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339298900  

Yes.

---

📁 include/boost/histogram/algorithm/empty.hpp

```diff
  18 |+ 
  19 |+   All cells in the histogram are checked, and this returns as soon as one cell is
  20 |+   found that is not empty.
```

> Username: HDembinski  
> Created_at: 2019-10-26 12:17:35 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339298051  

Better replace by: This algorithm has O(N) complexity, where N is the number of cells.

> Username: henryiii  
> Created_at: 2019-10-26 12:46:19 UTC  
> Updated_at: 2019-10-27 00:36:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#discussion_r339298857  

:+1:


---

## Comment 4

> Username: HDembinski  
> Created_at: 2019-10-26 12:19:05 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#issuecomment-546597754  

Otherwise looks very good. Almost ready to merge!

---

## Comment 5

> Username: henryiii  
> Created_at: 2019-10-26 17:56:19 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#issuecomment-546625444  

I think I see my mistake with the test. I can fix when I get home.

---

## Comment 6

> Username: henryiii  
> Created_at: 2019-10-27 00:38:28 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#issuecomment-546651474  

I'm quite surprised clang allowed `unsigned i = -1`... Anyway, that hid a logic error in test 2. I think we should pass tests now, fingers crossed.

---

## Comment 7

> Username: HDembinski  
> Created_at: 2019-10-27 08:42:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/245#issuecomment-546673554  

Thanks!

---
