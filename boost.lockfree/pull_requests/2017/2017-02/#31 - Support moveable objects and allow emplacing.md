# #31 Support moveable objects and allow emplacing [Open]

> Username: srjek  
> Created at: 2017-02-28 03:26:43 UTC  
> Updated at: 2024-01-26 03:59:09 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31  

This PR extends off of the months old #24 and it's recommended edits. Some additional work was done to make sure it also works with objects that are copy xor move constructible.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2017-03-02 06:47:24 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-283571067  

in general it looks good to me. i'd merge it, but it would be great if you could add some tests for it.

---

## Comment 2

> Username: srjek  
> Created_at: 2017-03-02 10:39:06 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-283618445  

Tests have been added to spsc_queue_test.cpp to verify the move-only and copy-only structures behave as expected. Also fixed a few issues I came across in the process.

---

## Comment 3

> Username: tnovotny  
> Created_at: 2017-07-22 06:28:13 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-317160010  

Can this please be go forward. I really need this and want to get rid of my own version [(#27)](https://github.com/boostorg/lockfree/pull/27).

---

## Review 4 [Commented]

> Username: tnovotny  
> Created_at: 2017-07-22 07:01:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/lockfree/pull/31#pullrequestreview-51617419  

📁 test/spsc_queue_test.cpp

```diff
 407 |     BOOST_REQUIRE(f.empty());
 408 | }
 409 |+ 
```

> Username: tnovotny  
> Created_at: 2017-07-22 06:57:35 UTC  
> Updated_at: 2017-07-22 11:02:30 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r128891366  

Shouldn't there also be a test for a move_only_type like unique_ptr?

> Username: timblechmann  
> Created_at: 2017-12-29 04:57:02 UTC  
> Updated_at: 2017-12-29 04:57:03 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r159025270  

+1

---

📁 test/spsc_queue_test.cpp

```diff
 474 |+ struct refcount_handle
 475 |+ {
 476 |+     int* p_refcount;
```

> Username: tnovotny  
> Created_at: 2017-07-22 07:01:47 UTC  
> Updated_at: 2017-07-22 07:01:58 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r128891433  

Not sure about the hungarian notation here. Consider renaming the variable.


---

## Comment 5

> Username: timblechmann  
> Created_at: 2017-07-22 09:29:05 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-317170496  

@tnovotny thanks for pinging me ... i'll look into it again over the next week

---

## Comment 6

> Username: tnovotny  
> Created_at: 2017-07-22 20:17:11 UTC  
> Updated_at: 2017-07-22 21:06:55 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-317207934  

@timblechmann it would also be nice if you could also look at my pull request, because if I remember correctly,  this will not suffice for move only types, as more of the existing API has to be disabled via `enable_if` to compile something like `spsc_queue<std::unique_ptr<..>>`.   
  
I checked. I get a compile errors on   
line 111 in `bool push(T const & t, T * buffer, size_t max_size)`.   
line 641 in `bool push(T const & t).`  
line 856 in `bool push(T const & t)`.  
line 664 in `bool consume_one(Functor & f)`  
...

---

## Comment 7

> Username: tnovotny  
> Created_at: 2017-08-02 18:02:29 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-319751023  

@timblechmann so, here's pinging you again.

---

## Comment 8

> Username: tnovotny  
> Created_at: 2017-08-26 16:29:31 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-325144082  

@timblechmann i find it really frustrating that you are showing no effort in maintaining this library. would you please give feedback on the outstanding pull requests.

---

## Comment 9

> Username: tnovotny  
> Created_at: 2017-09-11 19:22:22 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-328632152  

@timblechmann and another two weeks.

---

## Review 10 [Commented]

> Username: muggenhor  
> Created_at: 2017-09-14 09:30:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/lockfree/pull/31#pullrequestreview-62677715  

📁 include/boost/lockfree/spsc_queue.hpp

```diff
 117 |+ #ifndef BOOST_NO_CXX11_RVALUE_REFERENCES
 118 |+ 
 119 |+     bool push(T&& t, T * buffer, size_t max_size)
```

> Username: muggenhor  
> Created_at: 2017-09-14 08:42:28 UTC  
> Updated_at: 2017-09-14 09:30:18 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r138830964  

You can make this work on C++98 too with Boost.Move support by using `BOOST_RV_REF(T)` instead of `T&&` combined with `boost::move` instead of `std::move`.

> Username: timblechmann  
> Created_at: 2017-12-29 04:55:54 UTC  
> Updated_at: 2017-12-29 04:55:55 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r159025234  

i don't have a strong opinion on this: c++98 compilers are dying out and not so much new code is written for them. we just need to make sure, not to break any existing code by introducing unguarded c++1X constructs

---

📁 include/boost/lockfree/spsc_queue.hpp

```diff
 428 |+     {
 429 |+         if (boost::has_trivial_destructor<T>::value) {
 430 |+             return std::move(first, last, out); // will use memcpy if possible
```

> Username: muggenhor  
> Created_at: 2017-09-14 09:00:53 UTC  
> Updated_at: 2017-09-14 09:30:19 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r138835312  

Instead of duplicating this function, and using SFINAE to select the desired one, I would just have one implementation. This is safe because while, during overload resolution, the compiler will prefer move assignment, it will still fall back to copy assignment if no move assignment operator is available.  
  
Additionally I'd use [`boost::move`](http://www.boost.org/doc/libs/1_65_1/doc/html/move/move_algorithms.html) from `<boost/move/algorithm.hpp>` and `boost::move` from `<boost/move/utility_core.hpp>`. That will work without having to use the preprocessor (it's valid regardless of whether `BOOST_NO_CXX11_RVALUE_REFERENCES` is defined) and additionally supports types that implement move emulation too.

---

📁 include/boost/lockfree/spsc_queue.hpp

```diff
 513 | 
 514 |+ #ifndef BOOST_NO_CXX11_RVALUE_REFERENCES
 515 |+     bool push(T&& t)
```

> Username: muggenhor  
> Created_at: 2017-09-14 09:12:42 UTC  
> Updated_at: 2017-09-14 09:30:19 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r138838122  

Same as above: `BOOST_RV_REF` (also on C++98).  
  
This does not apply to the variadic templates below though.

---

📁 include/boost/lockfree/spsc_queue.hpp

```diff
 894 |     /** Pops one object from ringbuffer.
 895 |      *
 896 |      * \pre only one thread is allowed to pop data to the spsc_queue
```

> Username: muggenhor  
> Created_at: 2017-09-14 09:19:23 UTC  
> Updated_at: 2017-09-14 09:30:19 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r138839692  

Just dreaming here, but:  
  
It may be worth considering converting this argument-less `pop()` from returning `bool` to `boost::optional<T>`, which is convertible to bool. Because the current version, just returning `bool` is kind of worthless for everything except "some event has happened", similar to `boost::optional<void>` (if we had [regular void](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0146r0.html)). That would allow user code like this:  
```cpp  
while (auto item = queue.pop())  
{  
  // process item  
}  
```

> Username: timblechmann  
> Created_at: 2017-12-29 05:01:14 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r159025406  

true. one could only introduce a new API `queue.pop_optional()`. i'd merge a PR if you implement it :)


---

## Review 11 [Commented]

> Username: timblechmann  
> Created_at: 2017-12-29 05:00:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/lockfree/pull/31#pullrequestreview-85912411  

📁 include/boost/lockfree/spsc_queue.hpp

```diff
 865 |+      * \return true, if the push operation is successful.
 866 |+      *
 867 |+      * \note Thread-safe and wait-free
```

> Username: timblechmann  
> Created_at: 2017-12-29 05:00:12 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#discussion_r159025367  

maybe comment about the API which is not available if `BOOST_NO_CXX11_RVALUE_REFERENCES` is defined


---

## Comment 12

> Username: maierlars  
> Created_at: 2021-09-11 11:40:41 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-917392431  

Its 2021 now, what is stopping this PR from being merged? There is no emplace for this library, which is an important tool to create exception safe functions. This library is essentially dead?

---

## Comment 13

> Username: timblechmann  
> Created_at: 2021-09-11 14:13:14 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-917414457  

> Its 2021 now, what is stopping this PR from being merged? There is no emplace for this library, which is an important tool to create exception safe functions. This library is essentially dead?  
  
not dead, but unfortunately the author cannot spend much time on it and has no use cases himself for move semantics. furthermore there were two "competing" PRs, which eventually both went stale.  
  
happy to merge any merge-ready PR, though

---

## Comment 14

> Username: tnovotny  
> Created_at: 2021-09-11 15:58:21 UTC  
> Updated_at: 2021-09-11 17:01:44 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-917429828  

trying to get my PR into boost::lockfree was a very frustrating experience. my PR [(#27)](https://github.com/boostorg/lockfree/pull/27) was simply closed to go on with this one even though it did not support my use case of move only types such as unique_ptr. my changed PR [(#41)](https://github.com/boostorg/lockfree/pull/41) received no feedback. so to me, at the time, it felt like this library was stale and eventually I just gave up.

---

## Comment 15

> Username: jwdevel  
> Created_at: 2021-11-11 21:25:55 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-966636792  

> happy to merge any merge-ready PR, though  
  
Is #41 merge-ready? It has no conflicts.  
  
Or, if this present PR is preferred, would resolving its merge conflicts make it merge-ready?  
  
I say this just as a random person interested in this feature. I'd be willing to clean up this current PR if that's the desired route.

---

## Comment 16

> Username: tnovotny  
> Created_at: 2021-11-22 09:18:07 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-975314537  

that was about my experience. poor to no feedback on this topic

---

## Comment 17

> Username: xim  
> Created_at: 2021-11-23 10:26:24 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-976377114  

Just to weigh in, I'd also love to see this functionality merged

---

## Comment 18

> Username: jeremy-murphy  
> Created_at: 2023-10-22 22:52:11 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-1774222014  

Tim, what do you think now that C++98/03 is officially deprecated in Boost? https://pdimov.github.io/articles/phasing_out_cxx03.html  
  
My personal opinion is to stop actively supporting 98/03, so if there are users who need it then it's up to them to actively provide that support in the form of PRs, etc.  
  
And rather than surveying the community to inform a decision (which I did for another library), you'll find out pretty quickly if you just break it and see what happens.  :)

---

## Comment 19

> Username: intractabilis  
> Created_at: 2024-01-26 03:53:54 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-1911439634  

Are there any prospects of this being merged?

---

## Comment 20

> Username: timblechmann  
> Created_at: 2024-01-26 03:59:07 UTC  
> Url: https://github.com/boostorg/lockfree/pull/31#issuecomment-1911443233  

for 1.86 i'll drop c++03/c++11 support and #90 will be merged

---
