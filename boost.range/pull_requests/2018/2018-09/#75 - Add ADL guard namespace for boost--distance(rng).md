# #75 Add ADL guard namespace for boost::distance(rng) [Merged]

> Username: morinmorin  
> Created at: 2018-09-22 19:29:40 UTC  
> Updated at: 2018-09-23 17:21:46 UTC  
> Merged at: 2018-09-23 17:08:55 UTC  
> Closed at: 2018-09-23 17:08:55 UTC  
> Url: https://github.com/boostorg/range/pull/75  

Move `distance` function template into the ADL barrier namespace and pull the name by a using-directive. This fix should be OK, since [Boost.Range documentation](https://www.boost.org/doc/libs/develop/libs/range/doc/html/range/reference/concept_implementation/semantics/functions.html) explicitly forbids unqualified call to `distance`.  
- C++20 will have `std::distance(rng)` function template. This fix prevents ambiguity errors between `boost::distance(rng)` and `std::distance(rng)` caused by accidental ADL.  
- This fix may be necessary to fix boostorg/iterator#43.  
- Drive-by fix: it's OK to use `boost::distance(it1, it2)`, but [Boost.Iterator documentation](https://github.com/boostorg/iterator/blob/develop/doc/advance.rst) advertises only `boost::iterators::distance(it1, it2)`. So let's use it.

---

## Comment 1

> Username: morinmorin  
> Created_at: 2018-09-23 04:49:26 UTC  
> Url: https://github.com/boostorg/range/pull/75#issuecomment-423791777  

Testing on Travis CI fails, because  
  
- This PR needs to be applied in sync with boostorg/iterator#44.  
- But Boost.Iterator's PR was reverted, since that needs Boost.Range's PR.  
  
Oh, cyclic dependencies of PR's...

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-09-23 09:44:03 UTC  
> Url: https://github.com/boostorg/range/pull/75#issuecomment-423804149  

The change to Boost.Iterator is reapplied now.

---

## Comment 3

> Username: morinmorin  
> Created_at: 2018-09-23 12:50:56 UTC  
> Url: https://github.com/boostorg/range/pull/75#issuecomment-423814340  

> The change to Boost.Iterator is reapplied now.  
  
Thanks! I just reran the Travis testing of this PR (by force-pushing).

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-09-23 14:38:25 UTC  
> Url: https://github.com/boostorg/range/pull/75#issuecomment-423821238  

I had already re-run it. It passed except for the last job, which fails because libc++ wants random engines to have constexpr min/max, and the one in random_shuffle.cpp appears not to.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-09-23 17:08:46 UTC  
> Url: https://github.com/boostorg/range/pull/75#issuecomment-423831537  

OK, I'm going to merge this and see how it goes.

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-09-23 17:09:22 UTC  
> Url: https://github.com/boostorg/range/pull/75#issuecomment-423831573  

Would be nice if we could fix the (unrelated) Travis failure though.

---

## Comment 7

> Username: morinmorin  
> Created_at: 2018-09-23 17:21:43 UTC  
> Url: https://github.com/boostorg/range/pull/75#issuecomment-423832424  

Thanks for merging.  
  
> Would be nice if we could fix the (unrelated) Travis failure though.  
  
Definitely! (though I don't have any insight into how to fix that...)

---
