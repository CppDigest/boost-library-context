# #39 Apply_permutation [Merged]

> Username: zamazan4ik  
> Created at: 2017-05-19 02:42:25 UTC  
> Updated at: 2017-11-01 19:57:32 UTC  
> Merged at: 2017-11-01 19:57:31 UTC  
> Closed at: 2017-11-01 19:57:32 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39  

Hello.  
  
I found very useful FMPOV algorithm (https://blogs.msdn.microsoft.com/oldnewthing/20170104-00/?p=95115) .  
  
What do you think about it?

---

## Comment 1

> Username: zamazan4ik  
> Created_at: 2017-07-06 23:03:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#issuecomment-313542488  

What is missing in this PR? Do you have any suggestions?

---

## Review 2 [Commented]

> Username: mclow  
> Created_at: 2017-08-29 17:17:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/39#pullrequestreview-59316155  

📁 include/boost/algorithm/apply_permutation.hpp

```diff
  51 |+         {
  52 |+             Diff next = static_cast<Diff>(ind_begin[current]);
  53 |+             std::swap(item_begin[current], item_begin[next]);
```

> Username: mclow  
> Created_at: 2017-08-29 17:17:16 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r135855233  

Rather than `std::swap()`, you should bring swap into scope with a using decl, and then call it unadorned - thereby enabling ADL. Reference here: https://stackoverflow.com/questions/28130671/how-does-using-stdswap-enable-adl

> Username: zamazan4ik  
> Created_at: 2017-08-30 16:51:53 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r136127068  

Fixed.


---

## Review 3 [Commented]

> Username: mclow  
> Created_at: 2017-08-29 17:18:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/39#pullrequestreview-59316571  

📁 include/boost/algorithm/apply_permutation.hpp

```diff
  41 |+ void
  42 |+ apply_permutation(RandomAccessIterator1 item_begin, RandomAccessIterator1 item_end,
  43 |+                   RandomAccessIterator2 ind_begin)
```

> Username: mclow  
> Created_at: 2017-08-29 17:18:39 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r135855606  

The standard is moving away from "three iterator" algorithms (like `equal`, `mismatch`, etc) in favor of explicitly delimitating the second range.

> Username: zamazan4ik  
> Created_at: 2017-08-30 16:53:35 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r136127553  

Ok, i agree with you. I moved to four iterators interface. I found one interesting case: use can copy our implementation and use inside assert(std::distance(item_begin, item_end), std::distance(ind_begin, ind_end)) without interface changing.  
  
Fixed.


---

## Review 4 [Commented]

> Username: mclow  
> Created_at: 2017-08-29 17:21:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/39#pullrequestreview-59317472  

📁 doc/apply_permutation.qbk

```diff
  15 |+ The routine `apply_permutation` takes a item sequence and a order sequence. It reshuffles item sequence according to order sequence. Every value in order sequence means where the item comes from.
  16 |+ The routine `apply_reverse_permutation` takes a item sequence and a order sequence. It will reshuffle item sequence according to order sequence. Every value in order sequence means where the item goes to.
  17 |+ 
```

> Username: mclow  
> Created_at: 2017-08-29 17:21:39 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r135856384  

Are there any restrictions on the order sequence? I'm guessing that it needs to be exactly a permutation of the sequence [0, 1, .. n].  (no dups, no out-of-range values)

> Username: mclow  
> Created_at: 2017-08-29 17:23:17 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r135856823  

Also, you should mention that the order sequence gets permuted, too - that was not obvious to me until I read the code.

> Username: zamazan4ik  
> Created_at: 2017-08-30 16:53:58 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r136127662  

Fixed.


---

## Review 5 [Commented]

> Username: mclow  
> Created_at: 2017-08-29 17:25:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/39#pullrequestreview-59318542  

📁 include/boost/algorithm/apply_permutation.hpp

```diff
  37 |+ ///
  38 |+ /// \note Item sequence size should be equal to index size. Otherwise behavior is undefined.
  39 |+ ///       Complexity: O(N).
```

> Username: mclow  
> Created_at: 2017-08-29 17:25:05 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r135857308  

Given the two nested loops, I think you need to show why this is an O(N) algorithm.  At first blush, it looks like O(N^2) to me.

> Username: zamazan4ik  
> Created_at: 2017-08-30 17:07:31 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r136130926  

Complexity is O(N). Read about it here: https://blogs.msdn.microsoft.com/oldnewthing/20170109-00/?p=95145  
  
I think that there is no reason to write proof in doc. I will include reference to the original paper in doc (references to all six parts).  
  
I think it will be the best solution.


---

## Review 6 [Commented]

> Username: mclow  
> Created_at: 2017-08-29 19:04:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/39#pullrequestreview-59348523  

📁 doc/apply_permutation.qbk

```diff
  14 |+ 
  15 |+ The routine `apply_permutation` takes a item sequence and a order sequence. It reshuffles item sequence according to order sequence. Every value in order sequence means where the item comes from.
  16 |+ The routine `apply_reverse_permutation` takes a item sequence and a order sequence. It will reshuffle item sequence according to order sequence. Every value in order sequence means where the item goes to.
```

> Username: mclow  
> Created_at: 2017-08-29 19:04:24 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r135883849  

You might want to expand on the difference between `apply_permutation` and `apply_reverse_permutation`. Are they inverse operations?  (Running the example suggests not - but the explanation isn't clear to me)

> Username: zamazan4ik  
> Created_at: 2017-08-30 16:51:43 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r136127025  

Are these unclear?  
apply_permutation: Every value in order sequence means where the item comes from.  
apply_reverse_permutation: Every value in order sequence means where the item goes to.

> Username: zamazan4ik  
> Created_at: 2017-08-30 17:08:16 UTC  
> Updated_at: 2017-08-30 17:25:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/39#discussion_r136131104  

I thought that it's very clear explanation: "the item comes from" vs "the item goes to".


---
