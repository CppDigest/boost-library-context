# #144 Implement latch [Closed]

> Username: cmazakas  
> Created at: 2023-04-25 15:34:10 UTC  
> Updated at: 2023-05-16 14:53:06 UTC  
> Closed at: 2023-05-15 23:08:28 UTC  
> Url: https://github.com/boostorg/core/pull/144  

Add an implementation of C++20's `std::latch`  
  
https://en.cppreference.com/w/cpp/thread/latch  
  
Note, notifying on the condition variable without the lock being held triggers a "dubious" warning from helgrind.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2023-04-25 15:39:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/144#pullrequestreview-1400218544  

📁 doc/core.qbk

```diff
  84 | [include use_default.qbk]
  85 | [include verbose_terminate_handler.qbk]
  86 |+ [include latch.qbk]
```

> Username: pdimov  
> Created_at: 2023-04-25 15:39:39 UTC  
> Url: https://github.com/boostorg/core/pull/144#discussion_r1176705088  

These are in alphabetical order.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2023-04-25 15:40:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/144#pullrequestreview-1400220258  

📁 doc/latch.qbk

```diff
  36 |+     // block until all threads have reached this statement
  37 |+     l.arrive_and_wait();
  38 |+   });
```

> Username: pdimov  
> Created_at: 2023-04-25 15:40:33 UTC  
> Updated_at: 2023-04-25 15:40:34 UTC  
> Url: https://github.com/boostorg/core/pull/144#discussion_r1176706301  

There's `// do things` missing here.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2023-04-25 15:41:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/144#pullrequestreview-1400222402  

📁 doc/latch.qbk

```diff
  31 |+ boost::core::latch l(num_threads);
  32 |+ 
  33 |+ std::vector<std::thread> threads;
```

> Username: pdimov  
> Created_at: 2023-04-25 15:41:44 UTC  
> Url: https://github.com/boostorg/core/pull/144#discussion_r1176707790  

It will be better if the example compiles instead of being a fragment. So it would be better if this all is in a function.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-04-25 15:42:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/144#pullrequestreview-1400223827  

📁 doc/latch.qbk

```diff
  17 |+ The header `<boost/core/latch.hpp>` implements, in a portable way,
  18 |+ the C++20 `<latch>` header.
  19 |+ 
```

> Username: pdimov  
> Created_at: 2023-04-25 15:42:32 UTC  
> Url: https://github.com/boostorg/core/pull/144#discussion_r1176708719  

The C++11 requirement should be mentioned explicitly.


---

## Comment 5

> Username: Lastique  
> Created_at: 2023-04-25 15:43:46 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522023713  

IMO, this needs to go to Boost.Thread, not Boost.Core. Actually, [there already is one](https://github.com/boostorg/thread/blob/52b62ee7b029028126c4eaf36e377833b0666510/include/boost/thread/latch.hpp), so I'm inclined to close this.

---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2023-04-25 15:44:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/144#pullrequestreview-1400226779  

📁 include/boost/core/latch.hpp

```diff
   9 |+ 
  10 |+ #if defined(BOOST_NO_CXX11_HDR_CONDITION_VARIABLE)
  11 |+ BOOST_PRAGMA_MESSAGE("boost::core::latch requires <condition_variable>")
```

> Username: pdimov  
> Created_at: 2023-04-25 15:44:09 UTC  
> Updated_at: 2023-04-25 15:44:22 UTC  
> Url: https://github.com/boostorg/core/pull/144#discussion_r1176710667  

These messages are unnecessary. The inclusion of `<condition_variable>` below is enough to imply that this header requires it.


---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2023-04-25 15:45:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/144#pullrequestreview-1400229121  

📁 include/boost/core/latch.hpp

```diff
  79 |+     bool count_down_and_notify(
  80 |+         std::unique_lock<std::mutex> &lk, std::ptrdiff_t n)
  81 |+     {
```

> Username: pdimov  
> Created_at: 2023-04-25 15:45:29 UTC  
> Url: https://github.com/boostorg/core/pull/144#discussion_r1176712250  

If we are going to assert in the constructor, we should be consistent and assert the precondition here as well.


---

## Review 8 [Commented]

> Username: pdimov  
> Created_at: 2023-04-25 15:47:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/144#pullrequestreview-1400232075  

📁 doc/latch.qbk

```diff
 152 |+     [
 153 |+       [variablelist
 154 |+         [[Effects][Returns an implementation-defined number representing the maximum amount of waiters. Currently INT_MAX.]]
```

> Username: pdimov  
> Created_at: 2023-04-25 15:47:02 UTC  
> Url: https://github.com/boostorg/core/pull/144#discussion_r1176714233  

Why not PTRDIFF_MAX?


---

## Comment 9

> Username: pdimov  
> Created_at: 2023-04-25 15:53:53 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522038494  

Christian implemented this internally in the Unordered test suite, so I asked him to polish it and contribute it to Core (as it has no non-std dependencies and we already add emulation headers here).

---

## Comment 10

> Username: Lastique  
> Created_at: 2023-04-25 16:02:43 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522051375  

Why do we want to duplicate it in Core?

---

## Comment 11

> Username: pdimov  
> Created_at: 2023-04-25 16:05:57 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522055976  

Because otherwise we'll duplicate it in the test suites of all the libraries that need it.

---

## Comment 12

> Username: Lastique  
> Created_at: 2023-04-25 16:07:30 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522058071  

No, what I'm asking is why the tests can't use the implementation in Boost.Thread? If there's a problem with it, let's fix it there.

---

## Comment 13

> Username: pdimov  
> Created_at: 2023-04-25 16:08:56 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522060044  

A dependency on Boost.Thread just for this simple component is hardly justified.

---

## Comment 14

> Username: Lastique  
> Created_at: 2023-04-25 16:12:12 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522064606  

IMHO, there's nothing wrong with the dependency. The duplication is far less justified.  
  
Why do we write libraries in the first place, when we ourselves don't want to use them??

---

## Comment 15

> Username: cmazakas  
> Created_at: 2023-04-25 16:21:38 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522077546  

> IMHO, there's nothing wrong with the dependency. The duplication is far less justified.  
>   
> Why do we write libraries in the first place, when we ourselves don't want to use them??  
  
Well, one thing to keep in mind is that Core is slowly but surely growing into a C++17/20 polyfill library.  
  
Core already contains `std::span`, `<bit>` and has a `string_view` in its `detail` folder.  
  
Thinking about this as a vcpkg user, it's quite nice I'd be able to just install boost-core instead of boost-thread which is a heavier weight dependency to polyfill a C++20 stdlib feature.

---

## Comment 16

> Username: Lastique  
> Created_at: 2023-04-25 16:26:09 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522083663  

> Well, one thing to keep in mind is that Core is slowly but surely growing into a C++17/20 polyfill library.  
  
Which was never the intention.  
  
Re. `string_view`, I always was opposed to its addition.

---

## Comment 17

> Username: cmazakas  
> Created_at: 2023-04-25 16:40:58 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522101848  

> Re. `string_view`, I always was opposed to its addition.  
  
This is interesting to me.  
  
C++11 and 14 are still pretty popular standards in use today. `string_view` is a pretty great type that can exist easily in these versions of C++.  
  
Right now, I'm not aware of any other maintained/reliable implementations of `string_view` in Boost. In this same vein, it seems like Thread hasn't received much attention in recent years.  
  
Whatever intentions began for Core, the general evolution is tending towards implementing the small goodies from later C++ standards. `latch` fits this bill. It's simple, straight-forward and solves subtle implementation issues that people who hand-roll are likely to create.

---

## Comment 18

> Username: Lastique  
> Created_at: 2023-04-25 17:03:32 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1522130467  

> Right now, I'm not aware of any other maintained/reliable implementations of `string_view` in Boost.  
  
The canonical implementation is in Boost.Utility.  
  
Anyway, if other maintainers decide that this should be accepted, so be it. @glenfe, could you comment?

---

## Comment 19

> Username: glenfe  
> Created_at: 2023-04-27 11:27:08 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1525525540  

If this is only going to be used in the tests, I'd be inclined to just just keep using the one in Boost.Unordered (if not the one in Boost.Thread).   
  
If the one in Boost.Thread is going to be replaced with this (e.g. Thread dropping C++03, and aliasing boost::core::latch) then this being in Core is better.

---

## Comment 20

> Username: pdimov  
> Created_at: 2023-04-27 11:30:34 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1525529804  

The latter is probably not happening. Thread doesn't have a maintainer and is way too complex for drive-by maintenance.

---

## Comment 21

> Username: pdimov  
> Created_at: 2023-04-27 13:14:51 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1525677590  

With subsequent C++ standards adding nice things, we do have a need for a place where we put our "polyfills" when they meet certain criteria: relatively simple, no dependencies beyond the necessary (Assert and ThrowException), no deviation from standard semantics so no need for design reviews.  
  
There's no better place than Core at the moment, but it might indeed not be the right one.  
  
Maybe we need to have a separate repo/library, Boost.Compat, where these can go. (We already have Compatibility but it's obsolete and the name is a bit too long for my taste.)

---

## Comment 22

> Username: glenfe  
> Created_at: 2023-04-27 13:57:30 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1525744948  

Boost.Compat sounds like a good idea.   
  
Moreover, if we decide it is never for public consumption (e.g. types in there only used in implementation details, never on interfaces, or used in unit tests, ideally with a namespace that conveys the same) then we don't have to even think about a formal review for such a repository.

---

## Comment 23

> Username: pdimov  
> Created_at: 2023-04-27 14:13:44 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1525774306  

Sounds appealing but I think it would definitely be consumed by the public, so we should acknowledge it formally (and have proper docs).  
  
The use in interface is an interesting point though. I suppose you're thinking of e.g. `span`, which would therefore not belong there.

---

## Comment 24

> Username: glenfe  
> Created_at: 2023-04-27 15:06:14 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1525865512  

Yes (which is shared by Histogram and Beast right now and used on the interfaces of both).

---

## Comment 25

> Username: cmazakas  
> Created_at: 2023-04-27 16:09:03 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1525970387  

Should we temporarily put a pin in this PR and instead directly address the mailing list about Boost.Compat?  
  
I will incorporate Peter's feedback first, of course.

---

## Comment 26

> Username: Kojoley  
> Created_at: 2023-04-28 13:08:44 UTC  
> Url: https://github.com/boostorg/core/pull/144#issuecomment-1527540494  

> No, what I'm asking is why the tests can't use the implementation in Boost.Thread? If there's a problem with it, let's fix it there.  
> IMHO, there's nothing wrong with the dependency. The duplication is far less justified.  
  
1. Being in Boost.Thread makes it look like it requires using threads from Boost.Thread.  
2. There is a problem with the dependency on Boost.Thread -- it requires compilation and also drags even more dependencies. In CMake it could be solved by providing separate 'library'.  
  
I actually was under impression that the Core library is exactly for such things. Citing the readme:  
  
> Boost.Core, part of collection of the [Boost C++ Libraries](https://github.com/boostorg), is a collection of core utilities used by other Boost libraries. The criteria for inclusion is that the utility component be:  
> * simple,  
> * used by other Boost libraries, and  
> * not dependent on any other Boost modules except Core itself, Config, Assert, Static Assert, or Predef.

---
