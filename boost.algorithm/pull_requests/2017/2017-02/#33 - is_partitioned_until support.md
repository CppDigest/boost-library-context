# #33 is_partitioned_until support [Merged]

> Username: zamazan4ik  
> Created at: 2017-02-14 21:12:40 UTC  
> Updated at: 2017-05-04 01:15:09 UTC  
> Merged at: 2017-05-04 01:15:09 UTC  
> Closed at: 2017-05-04 01:15:09 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33  

Hello.  
  
Here is implementation for 'is_partitioned_until' algorithm.

---

## Review 1 [Commented]

> Username: mclow  
> Created_at: 2017-02-21 17:30:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/33#pullrequestreview-23019852  

📁 README.md

```diff
   1 |+ 
```

> Username: mclow  
> Created_at: 2017-02-21 17:30:44 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102267908  

What's this file doing here (with only a blank line in it)?

> Username: zamazan4ik  
> Created_at: 2017-02-21 22:02:15 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102329707  

It's trash. Already deleted.


---

## Review 2 [Commented]

> Username: mclow  
> Created_at: 2017-02-21 17:34:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/33#pullrequestreview-23020623  

📁 doc/is_partitioned_until.qbk

```diff
  12 |+ The header file 'is_partitioned_until.hpp' contains two variants of a single algorithm, `is_partitioned_until`. The algorithm tests to see if a sequence is partitioned according to a predicate; in other words, all the items in the sequence that satisfy the predicate are at the beginning of the sequence.
  13 |+ 
  14 |+ The routine `is_partitioned_until` takes a sequence and a predicate. It returns the first iterator 'it' in the sequence [first, last) for which is_partitioned(first, it, p) is false. Returns last if the entire sequence is partitioned.
```

> Username: mclow  
> Created_at: 2017-02-21 17:34:10 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102268657  

I think that inverting this description might be better.  Something like "It returns the last iterator `it` in the sequence `[begin, end)` for which the `is_partitioned(begin, it)` is true."   That way, it is clear that if the entire sequence is partitioned, it returns `end`, since that's the last iterator in the sequence.  
  
You might also want to note that every sequence of length < 2 is partitioned (or maybe I'll put that into the `is_partitioned` docs)

> Username: zamazan4ik  
> Created_at: 2017-02-21 22:18:05 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102333251  

Fixed.


---

## Review 3 [Commented]

> Username: mclow  
> Created_at: 2017-02-21 17:36:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/33#pullrequestreview-23021030  

📁 doc/is_partitioned_until.qbk

```diff
  56 |+ [heading Notes]
  57 |+ 
  58 |+ * `is_partitioned_until` returns iterator to the end for empty ranges, no matter what predicate is passed to test against.
```

> Username: mclow  
> Created_at: 2017-02-21 17:36:14 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102269030  

See comment above; this is also true for single-element ranges.

> Username: zamazan4ik  
> Created_at: 2017-02-21 22:17:28 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102333071  

Fixed. I also fixed 'is_partitioned' documentation.


---

## Review 4 [Commented]

> Username: mclow  
> Created_at: 2017-02-21 17:37:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/33#pullrequestreview-23021316  

📁 example/is_partitioned_until_example.cpp

```diff
  34 |+ {
  35 |+     std::vector<int> good({1, 2, 4});
  36 |+     std::vector<int> bad({1, 2, 3});
```

> Username: mclow  
> Created_at: 2017-02-21 17:37:34 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102269316  

Initializing a vector from an initializer-list is a C++11 thing.

> Username: mclow  
> Created_at: 2017-02-21 17:39:39 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102269809  

On the other hand, this is in an example...

> Username: zamazan4ik  
> Created_at: 2017-02-21 21:58:26 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102328844  

We have identical example in 'is_partitioned'. It's example, so i think, here we can use C++11.


---

## Review 5 [Commented]

> Username: mclow  
> Created_at: 2017-02-21 17:38:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/33#pullrequestreview-23021501  

📁 include/boost/algorithm/is_partitioned_until.hpp

```diff
  19 |+ 
  20 |+ /// \fn is_partitioned_until ( InputIterator first, InputIterator last, UnaryPredicate p )
  21 |+ /// \brief Tests to see if a sequence is partitioned according to a predicate
```

> Username: mclow  
> Created_at: 2017-02-21 17:38:13 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102269498  

This should have a better description.

> Username: zamazan4ik  
> Created_at: 2017-02-21 22:24:50 UTC  
> Updated_at: 2017-02-21 22:26:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#discussion_r102334691  

I only got description from is_partitioned.hpp. I think, here we can simply duplicate description from .qbk. Also updated 'is_partitioned.hpp'.


---

## Comment 6

> Username: mclow  
> Created_at: 2017-02-21 17:39:22 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#issuecomment-281418687  

This is nice work.   There's just a few nits.

---

## Comment 7

> Username: zamazan4ik  
> Created_at: 2017-02-21 22:27:42 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#issuecomment-281502666  

Ok. All issues, i think, fixed. Thank you a lot for review :)

---

## Comment 8

> Username: zamazan4ik  
> Created_at: 2017-04-08 21:50:33 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#issuecomment-292748122  

Hello. What about merging?

---

## Comment 9

> Username: zamazan4ik  
> Created_at: 2017-04-30 23:46:27 UTC  
> Url: https://github.com/boostorg/algorithm/pull/33#issuecomment-298265115  

@mclow Is there any problem with 'is_partitioned_until' ? Can you merge this?

---
