# #46 Fix 'detail' namespace opening for Borland [Merged]

> Username: tanzislam  
> Created at: 2017-08-27 14:03:05 UTC  
> Updated at: 2017-10-28 12:50:32 UTC  
> Merged at: 2017-10-26 17:28:25 UTC  
> Closed at: 2017-10-26 17:28:25 UTC  
> Url: https://github.com/boostorg/type_traits/pull/46  

In `type_with_alignment.hpp`, the `boost::detail` namespace is opened in line 28 just before starting the main `#ifndef __BORLANDC__`-block at line 30. The `detail` namespace is closed within the `#ifndef`-block at line 79. Highlight that namespace closing-brace with a comment for visibility.  
  
Furthermore, the main `#ifndef __BORLANDC__`-block has a `#else`-case beginning at line 209, which effectively resumes the `boost::detail` namespace at that point. This continuation of the namespace was missing a closing brace, causing a compilation failure with C++Builder (due to not finding `::boost::tt_align_ns::a16` in line 230, but merely finding `::boost::detail::tt_align_ns::a16`). Fix the problem by nesting the `detail` namespace within the `#ifndef __BORLANDC__`-block, to prevent it from leaking into the `#else`-block.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-09-06 18:19:00 UTC  
> Url: https://github.com/boostorg/type_traits/pull/46#issuecomment-327570326  

You're correct that this should be fixed, but wouldn't it be easier to move the opening of `namespace detail` inside the `#ifndef __BORLANC__` block where it belongs?

---

## Comment 2

> Username: tanzislam  
> Created_at: 2017-09-08 19:07:18 UTC  
> Url: https://github.com/boostorg/type_traits/pull/46#issuecomment-328188988  

Yes, that works too. I'll change it and update this PR.

---
