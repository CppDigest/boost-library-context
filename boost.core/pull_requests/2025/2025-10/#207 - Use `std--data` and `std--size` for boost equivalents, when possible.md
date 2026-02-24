# #207 Use `std::data` and `std::size` for boost equivalents, when possible [Closed]

> Username: Lastique  
> Created at: 2025-10-16 17:46:38 UTC  
> Updated at: 2025-10-19 18:56:49 UTC  
> Closed at: 2025-10-19 18:56:48 UTC  
> Url: https://github.com/boostorg/core/pull/207  

This avoids the potential ambiguity between `boost::` and `std::` functions when the user calls `data()` or `size()` unqualified and both alternatives are found via ADL.  
  
Fixes https://github.com/boostorg/core/issues/206.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-16 17:51:54 UTC  
> Url: https://github.com/boostorg/core/pull/207#issuecomment-3412096800  

I like having a test that checks exactly what's being reported as a problem; in this case, invoking `data(sp)` unqualified on a `boost::span` when `using std::data;` is in effect.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-10-16 17:57:14 UTC  
> Url: https://github.com/boostorg/core/pull/207#issuecomment-3412123039  

The added tests are for the changes in `data` and `size`. `span` is unchanged, so I didn't add the test for it. I can add a test for it as well, but it'll duplicate the tests added for `data` and `size`.

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-10-16 18:16:08 UTC  
> Url: https://github.com/boostorg/core/pull/207#issuecomment-3412214889  

> I can add a test for it as well, but it'll duplicate the tests added for `data` and `size`.  
  
Done.

---

## Comment 4

> Username: Lastique  
> Created_at: 2025-10-18 10:28:10 UTC  
> Url: https://github.com/boostorg/core/pull/207#issuecomment-3418186860  

Any objections to merging this?

---
