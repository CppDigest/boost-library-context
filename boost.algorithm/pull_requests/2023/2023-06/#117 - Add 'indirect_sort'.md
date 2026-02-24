# #117 Add 'indirect_sort' [Open]

> Username: mclow  
> Created at: 2023-06-15 01:36:09 UTC  
> Updated at: 2023-06-26 03:32:21 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117  



---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2023-06-15 08:47:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/117#pullrequestreview-1481004277  

Looks potentially useful.  
  
Added some ideas for improving the description and pointed out a few places for improvement/typo-fixing.

📁 doc/indirect_sort.qbk

```diff
  12 |+ There are times that you want a sorted version of a sequence, but for some reason or another, you don't really want to sort them.  Maybe the elements in the sequence are non-copyable (or non-movable), or the sequence is const, or they're just really expensive to move around.   An example of this might be a sequence of records from a database.
  13 |+ 
  14 |+ Nevertheless, you might want to sort them.  That's where indirect sorting comes in.  In a "normal" sort, the elements of the sequence to be sorted are shuffled in place.  In indirect sorting, the elements are unchanged, but the sort algorithm returns to you a "permutation" of the elements that, when applied, will leave the elements in the sequence in a sorted order.
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:31:52 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230644315  

How about this a bit shorter wording especially avoiding to mention the need to sort twice:  
  
```diff  
-Nevertheless, you might want to sort them.  That's where indirect sorting comes in.  In a "normal" sort, the elements of the sequence to be sorted are shuffled in place.  In indirect sorting, the elements are unchanged, but the sort algorithm returns to you a "permutation" of the elements that, when applied, will leave the elements in the sequence in a sorted order.  
+There are times that you want a sorted version of a sequence, but for some reason you don't want to modify it.  Maybe the elements in the sequence can't be moved/copied, e.g. the sequence is const, or they're just really expensive to move around.   An example of this might be a sequence of records from a database.  
+That's where indirect sorting comes in.  In a "normal" sort, the elements of the sequence to be sorted are shuffled in place.  In indirect sorting, the elements are unchanged, but the sort algorithm returns a "permutation" of the elements that, when applied, will put the elements in the sequence in a sorted order.  
```  
  
Are the double-spaces after each sentence intended?

---

📁 doc/indirect_sort.qbk

```diff
  14 |+ Nevertheless, you might want to sort them.  That's where indirect sorting comes in.  In a "normal" sort, the elements of the sequence to be sorted are shuffled in place.  In indirect sorting, the elements are unchanged, but the sort algorithm returns to you a "permutation" of the elements that, when applied, will leave the elements in the sequence in a sorted order.
  15 |+ 
  16 |+ Say you have a sequence `[first, last)` of 1000 items that are expensive to swap:
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:32:57 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230645709  

```diff  
-Say you have a sequence `[first, last)` of 1000 items that are expensive to swap:  
+Assume a sequence `[first, last)` of 1000 items that are expensive to swap:  
```


📁 include/boost/algorithm/indirect_sort.hpp

```diff
  17 |+ #include <algorithm>        // for std::sort (and others)
  18 |+ #include <functional>       // for std::less
  19 |+ #include <vector>           // for std:;vector
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:34:16 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230647324  

Typo:   
```diff  
-#include <vector>           // for std:;vector  
+#include <vector>           // for std::vector  
```  
  
But is that comment really required?

---

📁 include/boost/algorithm/indirect_sort.hpp

```diff
  13 |+ 
  14 |+ #ifndef BOOST_ALGORITHM_IS_INDIRECT_SORT
  15 |+ #define BOOST_ALGORITHM_IS_INDIRECT_SORT
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:35:14 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230648411  

Unusual include guard. Why not `BOOST_ALGORITHM_INDIRECT_SORT`?

---

📁 include/boost/algorithm/indirect_sort.hpp

```diff
  65 |+ /// \returns a permutation of the elements in the range [first, last)
  66 |+ ///     such that when the permutation is applied to the sequence,
  67 |+ ///     the result is sorted according to the predicate pred.
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:36:28 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230649880  

```diff  
-///     the result is sorted according to the predicate pred.  
+///     the result is sorted in non-descending order.  
```

---

📁 include/boost/algorithm/indirect_sort.hpp

```diff
  62 |+ }
  63 |+ 
  64 |+ /// \fn indirect_sort (RAIterator first, RAIterator las )
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:36:45 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230650240  

```diff  
-/// \fn indirect_sort (RAIterator first, RAIterator las )  
+/// \fn indirect_sort (RAIterator first, RAIterator last)  
```

---

📁 include/boost/algorithm/indirect_sort.hpp

```diff
  54 |+ ///
  55 |+ template <typename RAIterator, typename Pred>
  56 |+ std::vector<size_t> indirect_sort (RAIterator first, RAIterator last, Pred pred) {
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:38:25 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230652671  

```diff  
-std::vector<size_t> indirect_sort (RAIterator first, RAIterator last, Pred pred) {  
+Permutation indirect_sort (RAIterator first, RAIterator last, Pred pred) {  
```

---

📁 include/boost/algorithm/indirect_sort.hpp

```diff
  71 |+ ///
  72 |+ template <typename RAIterator>
  73 |+ std::vector<size_t> indirect_sort (RAIterator first, RAIterator last) {
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:38:45 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230653111  

```diff  
-std::vector<size_t> indirect_sort (RAIterator first, RAIterator last) {  
+Permutation indirect_sort (RAIterator first, RAIterator last) {  
```


📁 test/indirect_sort_test.cpp

```diff
  74 |+ 
  75 |+ void test_sort () {
  76 |+     BOOST_CXX14_CONSTEXPR int num[] = { 1,3,5,7,9, 2, 4, 6, 8, 10 };
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:40:49 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230655921  

```diff  
-    BOOST_CXX14_CONSTEXPR int num[] = { 1,3,5,7,9, 2, 4, 6, 8, 10 };  
+    int num[] = { 1,3,5,7,9, 2, 4, 6, 8, 10 };  
```  
or `int       *first  = &num[0];` is invalid isn't it?

---

📁 test/indirect_sort_test.cpp

```diff
  25 |+ // A permutation of size N is a sequence of values in the range [0..N)
  26 |+ // such that no value appears more than once in the permutation.
  27 |+ bool isa_permutation(Permutation p, size_t N) {
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:41:33 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230656888  

```diff  
-bool isa_permutation(Permutation p, size_t N) {  
+bool is_a_permutation(Permutation p, size_t N) {  
```  
is more readable.

---

📁 test/indirect_sort_test.cpp

```diff
  95 |+     }
  96 |+     
  97 |+ BOOST_AUTO_TEST_CASE( test_main )
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:43:39 UTC  
> Updated_at: 2023-06-15 08:47:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230659566  

Why that extra method and not using `BOOST_AUTO_TEST_CASE(test_sort)` directly?

> Username: mclow  
> Created_at: 2023-06-18 17:35:16 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1233346980  

Because I expect there to be more test cases in the future.

> Username: Flamefire  
> Created_at: 2023-06-20 07:02:39 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1234824835  

But the whole idea of `BOOST_AUTO_TEST_CASE` is that you simply "decorate" each test case with that and NOT have a "main" function. By default it will run each such function sequentially even allowing you to filter test based on their name from the CLI.  
  
-->   
  
```  
BOOST_AUTO_TEST_CASE( test_sort ){  
...  
}  
  
BOOST_AUTO_TEST_CASE( test_indirect_stable_sort ){  
...  
}  
```

---

📁 test/indirect_sort_test.cpp

```diff
   1 |+ /* 
   2 |+    Copyright (c) Marshall Clow 2011-2012.
```

> Username: Flamefire  
> Created_at: 2023-06-15 08:44:27 UTC  
> Updated_at: 2023-06-15 08:47:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1230660626  

```diff  
-   Copyright (c) Marshall Clow 2011-2012.  
+   Copyright (c) Marshall Clow 2023.  
```


---

## Review 2 [Changes requested]

> Username: Flamefire  
> Created_at: 2023-06-21 07:55:03 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/algorithm/pull/117#pullrequestreview-1490019636  

📁 include/boost/algorithm/indirect_sort.hpp

```diff
 135 |+ }
 136 |+ 
 137 |+ /// \fn indirect_partial_sort (RAIterator first, RAIterator last)
```

> Username: Flamefire  
> Created_at: 2023-06-21 07:54:30 UTC  
> Updated_at: 2023-06-21 07:55:03 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1236547352  

C&P mistake in the signature inside this (and below) docstrings

---

📁 include/boost/algorithm/indirect_sort.hpp

```diff
 156 |+ /// \returns a permutation of the elements in the range [first, last)
 157 |+ ///     such that when the permutation is applied to the sequence,
 158 |+ ///     the result is sorted according to the predicate pred.
```

> Username: Flamefire  
> Created_at: 2023-06-21 07:54:50 UTC  
> Updated_at: 2023-06-21 07:55:03 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1236547808  

Similar C&P mistake in signature and description.

> Username: mclow  
> Created_at: 2023-06-22 00:12:15 UTC  
> Url: https://github.com/boostorg/algorithm/pull/117#discussion_r1237842594  

Nice catch - thanks!


---
