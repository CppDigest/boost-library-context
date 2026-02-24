# #70 Removed deprecated std::allocator<void> [Closed]

> Username: bansan85  
> Created at: 2019-10-28 12:07:07 UTC  
> Updated at: 2020-05-31 14:12:10 UTC  
> Closed at: 2020-05-31 14:12:09 UTC  
> Url: https://github.com/boostorg/format/pull/70  

Fix https://github.com/boostorg/format/issues/67  
  
Based on patch from LibreOffice  
https://cgit.freedesktop.org/libreoffice/core/commit/?id=677c8de4fa79cd9b278b142013ba7f1c9e4e41c3  
  
and pull request from https://github.com/boostorg/bimap/pull/15

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-10-28 12:56:56 UTC  
> Url: https://github.com/boostorg/format/pull/70#issuecomment-546933337  

# [Codecov](https://codecov.io/gh/boostorg/format/pull/70?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@894f465`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/format/pull/70/graphs/tree.svg?width=650&token=9WwfaSgmDb&height=150&src=pr)](https://codecov.io/gh/boostorg/format/pull/70?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #70   +/-   ##  
==========================================  
  Coverage           ?   67.73%             
==========================================  
  Files              ?       10             
  Lines              ?      877             
  Branches           ?      300             
==========================================  
  Hits               ?      594             
  Misses             ?      111             
  Partials           ?      172  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/format/pull/70?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/format/alt\_sstream\_impl.hpp](https://codecov.io/gh/boostorg/format/pull/70/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvYWx0X3NzdHJlYW1faW1wbC5ocHA=) | `23.52% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/format/pull/70?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/format/pull/70?src=pr&el=footer). Last update [894f465...e47eef3](https://codecov.io/gh/boostorg/format/pull/70?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2020-03-04 16:55:45 UTC  
> Url: https://github.com/boostorg/format/pull/70#issuecomment-594658653  

Likely need a CI test variant that uses BOOST_NO_CXX11_ALLOCATOR to prove this out.  In fact this is probably true of many of the preprocessor branches around boost... will consider that.  Thanks for the submit; I've been bootstrapping a new startup so sorry for the delay.

---

## Review 3 [Commented]

> Username: jwakely  
> Created_at: 2020-03-30 11:41:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/format/pull/70#pullrequestreview-383770092  

📁 include/boost/format/alt_sstream_impl.hpp

```diff
 258 |+   #ifdef BOOST_NO_CXX11_ALLOCATOR
 259 |                     void *vdptr = alloc_.allocate(new_size, is_allocated_? oldptr : 0);
 260 |+   #else
```

> Username: jwakely  
> Created_at: 2020-03-30 11:41:01 UTC  
> Updated_at: 2020-03-30 11:41:02 UTC  
> Url: https://github.com/boostorg/format/pull/70#discussion_r400125118  

Is this combination possible? I would not expect the RW lib without class template partial specializations to support `allocator_traits` at all.


---

## Review 4 [Commented]

> Username: jwakely  
> Created_at: 2020-03-30 11:43:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/format/pull/70#pullrequestreview-383771626  

The title of the pull request seems wrong, this has nothing to do with `std::allocator<void>`, it's the fact that `std::allocator<T>::allocate` no longer accepts a hint argument in C++20.

---

## Comment 5

> Username: jwakely  
> Created_at: 2020-03-30 14:13:56 UTC  
> Url: https://github.com/boostorg/format/pull/70#issuecomment-606024926  

And shouldn't line 44 get fixed too?

---

## Comment 6

> Username: glenfe  
> Created_at: 2020-05-31 14:12:09 UTC  
> Url: https://github.com/boostorg/format/pull/70#issuecomment-636476817  

Covered by 5884c3d8140cdcd2d1bef374fcc216244d5faaa5.

---
