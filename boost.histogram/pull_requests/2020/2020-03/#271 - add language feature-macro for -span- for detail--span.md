# #271 [Fix] add language feature-macro for <span> for detail::span [Closed]

> Username: JonasToth  
> Created at: 2020-03-05 10:58:59 UTC  
> Updated at: 2020-03-09 14:10:39 UTC  
> Closed at: 2020-03-07 14:06:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/271  

This commit fixes an issue with newer clang/libc++-versions supporting C++2a.  
When compiling in C++17 (or earlier) mode, the compiler and libc++ do not expose  
the `<span>` include. The logic for `detail::span` to include `<span>`  
instead of providing an own implementation does only check if the  
include `<span>` exists. It exists, but it is not exposed.  
Therefore the compilation fails without this fix complaining about the missing `span`.  
  
Fixes #270

---

## Comment 1

> Username: henryiii  
> Created_at: 2020-03-05 12:24:19 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#issuecomment-595202898  

This macro is supposed to be defined *in* `<span>`, so the check needs to be a little more complex. It should happen after the include.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2020-03-06 08:52:42 UTC  
> Updated_at: 2020-03-06 08:52:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#issuecomment-595667060  

Thank you very much. The failing test is unrelated to this patch, I will look into it tonight and patch this and then merge.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2020-03-06 08:58:08 UTC  
> Updated_at: 2020-03-06 08:58:37 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#issuecomment-595669037  

> This macro is supposed to be defined _in_ `<span>`, so the check needs to be a little more complex. It should happen after the include.  
  
According to the docs,  
https://en.cppreference.com/w/cpp/utility/feature_test  
we need to include either `<version>` or `<span>` to have the feature test macro, `<version>` seems to be more straight forward, but also has to be protected with a `__has_include`. So yes, this test needs to be more complex.

---

## Review 4 [Commented]

> Username: henryiii  
> Created_at: 2020-03-06 19:36:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/271#pullrequestreview-370577417  

📁 include/boost/histogram/detail/span.hpp

```diff
   8 | #define BOOST_HISTOGRAM_DETAIL_SPAN_HPP
```

> Username: henryiii  
> Created_at: 2020-03-06 19:36:56 UTC  
> Updated_at: 2020-03-06 19:36:57 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#discussion_r389101563  

```diff  
+#if __has_include(<version>)  
+#include <version>  
+#endif  
```  
  
Something like this, perhaps?

> Username: HDembinski  
> Created_at: 2020-03-07 13:44:40 UTC  
> Updated_at: 2020-03-07 13:44:41 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#discussion_r389255654  

Yes


---

## Comment 5

> Username: HDembinski  
> Created_at: 2020-03-07 14:06:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#issuecomment-596091989  

@JonasToth It turns out I already had a fix for this in my private fork, which I had forgotten to upload to the main repository. I updated the develop branch now which has the fix for this issue.

---

## Comment 6

> Username: JonasToth  
> Created_at: 2020-03-07 21:00:25 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#issuecomment-596137050  

Perfect, thank you! I didnt had time to recheck yet, but as its fixed now, even better :)  
  
Do you know, when the next boost-release will be?

---

## Comment 7

> Username: HDembinski  
> Created_at: 2020-03-09 14:10:39 UTC  
> Url: https://github.com/boostorg/histogram/pull/271#issuecomment-596546576  

Next release is in April  
https://github.com/boostorg/wiki/wiki/Releases%3A-Schedule

---
