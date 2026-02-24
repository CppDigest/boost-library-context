# #1 Implement latch [Merged]

> Username: cmazakas  
> Created at: 2023-05-14 19:54:12 UTC  
> Updated at: 2023-05-16 14:52:47 UTC  
> Merged at: 2023-05-15 23:08:26 UTC  
> Closed at: 2023-05-15 23:08:26 UTC  
> Url: https://github.com/boostorg/compat/pull/1  

With this PR, we can now also close https://github.com/boostorg/core/pull/144

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2023-05-14 22:59:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/1#pullrequestreview-1425615476  

📁 doc/compat.adoc

```diff
  17 | 
  18 | include::compat/overview.adoc[]
  19 |+ include::compat/latch.adoc[]
```

> Username: pdimov  
> Created_at: 2023-05-14 22:59:12 UTC  
> Updated_at: 2023-05-14 22:59:13 UTC  
> Url: https://github.com/boostorg/compat/pull/1#discussion_r1193222279  

Should be after the revision history.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2023-05-14 22:59:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/1#pullrequestreview-1425615588  

📁 doc/compat/latch.adoc

```diff
   6 |+ 
   7 |+ [#latch]
   8 |+ # Latch
```

> Username: pdimov  
> Created_at: 2023-05-14 22:59:51 UTC  
> Updated_at: 2023-05-14 22:59:52 UTC  
> Url: https://github.com/boostorg/compat/pull/1#discussion_r1193222390  

Section headers should probably be the header names, `<boost/core/latch.hpp>` in this case.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2023-05-14 23:00:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/1#pullrequestreview-1425615671  

📁 doc/compat/latch.adoc

```diff
   9 |+ :idprefix: latch_
  10 |+ 
  11 |+ The header `<boost/core/latch.hpp>` implements, in a portable way, the C++20
```

> Username: pdimov  
> Created_at: 2023-05-14 23:00:27 UTC  
> Updated_at: 2023-05-14 23:00:28 UTC  
> Url: https://github.com/boostorg/compat/pull/1#discussion_r1193222435  

A subsection is missing here, I suggest `## Description`.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-05-14 23:01:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/1#pullrequestreview-1425615766  

📁 doc/compat/latch.adoc

```diff
 131 |+ ```
 132 |+ 
 133 |+ Returns an implementation-defined number representing the maximum amount of waiters. Currently INT_MAX.
```

> Username: pdimov  
> Created_at: 2023-05-14 23:01:06 UTC  
> Url: https://github.com/boostorg/compat/pull/1#discussion_r1193222541  

Same question as before; why not PTRDIFF_MAX?

> Username: cmazakas  
> Created_at: 2023-05-15 15:15:22 UTC  
> Url: https://github.com/boostorg/compat/pull/1#discussion_r1193989944  

Originally, I only checked what libstdc++ did which was use INT_MAX and I wanted to keep parity with that. But both MS STL and libc++ seem to use PTRDIFF_MAX.  
  
I don't really have a good reason for sticking with INT_MAX other than "libstdc++ does it". We can change this to PTRDIFF_MAX to keep us in line with the other stdlib implementations if you'd like.  
  
I suppose in my mind, I was thinking a user may not need a latch with a count that high but who knows. I imagine this class to be used where the count == thread count and it'd be pretty tough to have that many threads.

> Username: pdimov  
> Created_at: 2023-05-15 15:17:42 UTC  
> Updated_at: 2023-05-15 15:17:43 UTC  
> Url: https://github.com/boostorg/compat/pull/1#discussion_r1193993063  

Since the type is `ptrdiff_t`, having the max value be something else is odd and would need better justification than "but the user doesn't need that many threads."


---

## Comment 5

> Username: pdimov  
> Created_at: 2023-05-14 23:02:08 UTC  
> Url: https://github.com/boostorg/compat/pull/1#issuecomment-1547021149  

Looks like the test is missing a `<threading>multi>` requirement.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-05-14 23:03:09 UTC  
> Url: https://github.com/boostorg/compat/pull/1#issuecomment-1547021378  

`CMakeLists.txt` needs to be regenerated to pick up the new dependency on Assert.

---

## Comment 7

> Username: pdimov  
> Created_at: 2023-05-14 23:04:13 UTC  
> Url: https://github.com/boostorg/compat/pull/1#issuecomment-1547021613  

And I think `test/CMakeLists.txt` needs `Threads::Threads` as a dependency.

---

## Comment 8

> Username: pdimov  
> Created_at: 2023-05-15 18:27:09 UTC  
> Url: https://github.com/boostorg/compat/pull/1#issuecomment-1548350884  

Just some Windows failures to take care of, and `set(THREADS_PREFER_PTHREAD_FLAG ON)`.

---
