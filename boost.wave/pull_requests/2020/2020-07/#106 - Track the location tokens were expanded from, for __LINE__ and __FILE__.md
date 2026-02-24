# #106 Track the location tokens were expanded from, for __LINE__ and __FILE__ [Merged]

> Username: jefftrull  
> Created at: 2020-07-21 03:54:06 UTC  
> Updated at: 2020-08-16 18:54:30 UTC  
> Merged at: 2020-08-16 18:54:29 UTC  
> Closed at: 2020-08-16 18:54:30 UTC  
> Url: https://github.com/boostorg/wave/pull/106  

An (optional) extra position field is added to token_data and set for  
any identifier token created from a macro expansion. This information  
is used to correctly calculate the filename and line number.  
  
If merged, this will resolve #94.  
  
NOT intended for Boost 1.74.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2020-07-21 03:55:33 UTC  
> Url: https://github.com/boostorg/wave/pull/106#issuecomment-661616771  

With these changes we get the same answers as gcc - including on the difficult test case from the standard proposal (which I made into a unit test).

---

## Review 2 [Commented]

> Username: hkaiser  
> Created_at: 2020-07-21 15:58:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/106#pullrequestreview-452604454  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
2105 |+     }
2106 |+ }
2107 |+ 
```

> Username: hkaiser  
> Created_at: 2020-07-21 15:58:57 UTC  
> Url: https://github.com/boostorg/wave/pull/106#discussion_r458209836  

Will all tokens receive the same position information? Should they receive the corresponding column information as well?

> Username: jefftrull  
> Created_at: 2020-07-21 17:03:35 UTC  
> Url: https://github.com/boostorg/wave/pull/106#discussion_r458252374  

IMO, "no" on both. From my investigation, only `__LINE__` and `__FILE__` need to make use of the "position where expanded" information. For implementation reasons general identifiers do as well, in case they are queued instead of immediately expanded - they must pass their own expand positions for use by any `__LINE__` or `__FILE__` macros in their own expansion. That's one of the problems with `BAR` in the test case that triggered this project - it is a function-like macro whose name token and arguments come from separate macro expansions.  
  
As for the columns, I haven't seen any cases where the actual column number is needed. The `expand_pos` fields do capture it, but we don't use them.

> Username: hkaiser  
> Created_at: 2020-07-21 17:47:17 UTC  
> Updated_at: 2020-07-21 17:47:18 UTC  
> Url: https://github.com/boostorg/wave/pull/106#discussion_r458279207  

@jefftrull thanks for the explanations, makes sense. As for columns, they are used for error messages, I believe.


---

## Review 3 [Approved]

> Username: hkaiser  
> Created_at: 2020-07-21 16:00:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/106#pullrequestreview-452605534  

Nice solution! Thanks for your work on this!

---
