# #395 Include negated assertion_result messages: [Open]

> Username: krwalker  
> Created at: 2023-09-06 20:56:12 UTC  
> Updated at: 2024-05-14 16:19:00 UTC  
> Url: https://github.com/boostorg/test/pull/395  

Given a helper function `isEqualCaseInsensitive()` returning helpful detail in the `assertion_result` message, negation in a `BOOST_CHECK()` results in that detail being discarded.  
  
This feature preserves that detail in the negated case.  
  
For example:  
  
`BOOST_CHECK(isEqualCaseInsensitive('z', 'Z'))` included detail like `[z isEqualCaseInsensitiveTo Z]`  
  
`BOOST_CHECK(!isEqualCaseInsensitive('z', 'Z'))` discarded that detail until this revision, which now includes it: `NOT([z isEqualCaseInsensitiveTo Z])`

---
