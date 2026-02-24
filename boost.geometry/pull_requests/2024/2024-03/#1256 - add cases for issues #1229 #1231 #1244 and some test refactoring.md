# #1256 [test] add cases for issues #1229 #1231 #1244 and some test refactoring [Merged]

> Username: barendgehrels  
> Created at: 2024-03-03 22:36:26 UTC  
> Updated at: 2024-04-01 11:29:04 UTC  
> Merged at: 2024-03-13 09:30:47 UTC  
> Closed at: 2024-03-13 09:30:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1256  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-03 22:38:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1256#pullrequestreview-1913138523  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
  37 |+ // Change compiler defines to constexpr bools
  38 |+ // to make conditions more readable
  39 |+ // and to always compile all code.
```

> Username: barendgehrels  
> Created_at: 2024-03-03 22:38:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1256#discussion_r1510416218  

This is done in `difference.cpp` only, until now.  
If you agree, I'll apply it in the others too. And we can apply it everywhere.  
Advantages:  
* `if` i/o `ifdef`  
* code is always compiled  
* easier to read  
  
Also, we can harmonize it with the removing of the rescaling, as done in this source file.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-03 22:39:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1256#pullrequestreview-1913138760  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
  70 | 
  44 |- template <typename P>
  71 |+ template <typename P, bool ClockWise>
```

> Username: barendgehrels  
> Created_at: 2024-03-03 22:39:39 UTC  
> Updated_at: 2024-03-03 22:39:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1256#discussion_r1510416454  

I added `ccw` tests for difference, it was not earlier there. Most cases work, but a few of them don't - they are now conditionally


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-03 22:41:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1256#pullrequestreview-1913139140  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 525 |-             4, 16, 0.833333, 4, 20, 2.833333);
 527 |+         using box = bg::model::box<P>;
 528 |+         using ring = bg::model::ring<P>;
```

> Username: barendgehrels  
> Created_at: 2024-03-03 22:41:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1256#discussion_r1510416832  

changes in the block below are mainly whitespace


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-03 22:41:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1256#pullrequestreview-1913139316  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 621 |+     }
 622 |+ 
 623 |+     if BOOST_GEOMETRY_CONSTEXPR(! is_ccw || test_failures)
```

> Username: barendgehrels  
> Created_at: 2024-03-03 22:41:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1256#discussion_r1510417001  

@awulkiew I assume this is fine, and an else branch is not needed in such cases


---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2024-03-12 12:13:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1256#pullrequestreview-1930885191  

Thanks, this will help tracking the bugs in setops.

---
