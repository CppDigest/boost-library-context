# #265 Feature/nonconst set visit [Merged]

> Username: joaquintides  
> Created at: 2024-07-15 08:06:16 UTC  
> Updated at: 2024-07-16 17:57:51 UTC  
> Merged at: 2024-07-16 17:50:08 UTC  
> Closed at: 2024-07-16 17:50:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/265  

Addresses #260.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-15 08:17:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#issuecomment-2227938026  

An automated preview of the documentation is available at [https://265.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://265.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 2 [Commented]

> Username: cmazakas  
> Created_at: 2024-07-15 18:49:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/265#pullrequestreview-2178425103  

📁 test/cfoa/visit_tests.cpp

```diff
 993 |+   struct mutable_pair_hash
 994 |+   {
 995 |+     using is_transparent = void;
```

> Username: cmazakas  
> Created_at: 2024-07-15 18:49:17 UTC  
> Updated_at: 2024-07-15 18:49:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#discussion_r1678255216  

Weren't there conversations about having this become `using is_transparent = std::true_type:` and deprecating `= void;`?

> Username: joaquintides  
> Created_at: 2024-07-16 09:29:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#discussion_r1679067558  

It is `is_avalanching` where we've made this change. As for `is_transparent`, its behavior is dictated by the standard, and we're using `using is_transparent = void` elsewhere in the tests.

> Username: cmazakas  
> Created_at: 2024-07-16 17:29:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#discussion_r1679797946  

Ah, thanks. Got my wires crossed there.


---

## Review 3 [Commented]

> Username: cmazakas  
> Created_at: 2024-07-15 18:52:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/265#pullrequestreview-2178433588  

📁 test/cfoa/visit_tests.cpp

```diff
1043 |+         {
1044 |+           std::unique_lock<std::mutex> lk(m);
1045 |+           cv.wait(lk, [&] { return finish; });
```

> Username: cmazakas  
> Created_at: 2024-07-15 18:52:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#discussion_r1678261530  

I think we should have a latch component either internally or in Compat we can use here. I'm assuming the intention here was to block all the spawned threads until we're ready for them to actually run, right?

> Username: joaquintides  
> Created_at: 2024-07-16 09:30:18 UTC  
> Updated_at: 2024-07-16 09:32:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#discussion_r1679068226  

You're absolutely right! Changed it to use `boost::compat::latch`.  
  
As for the intent, it's not to block them until they are allowed to run, but to block them inside the visitation function until we tell them to go on --this way, if visitation locking is shared, we can detect that `in_visit` is > 1 (and report an error).


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-07-16 09:37:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#issuecomment-2230457429  

An automated preview of the documentation is available at [https://265.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://265.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-07-16 17:57:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/265#issuecomment-2231497975  

An automated preview of the documentation is available at [https://265.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://265.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
