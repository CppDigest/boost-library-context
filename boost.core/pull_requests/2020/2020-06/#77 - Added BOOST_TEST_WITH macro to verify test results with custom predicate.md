# #77 Added BOOST_TEST_WITH macro to verify test results with custom predicate [Merged]

> Username: breese  
> Created at: 2020-06-06 16:58:59 UTC  
> Updated at: 2020-06-07 12:44:47 UTC  
> Merged at: 2020-06-07 12:44:47 UTC  
> Closed at: 2020-06-07 12:44:47 UTC  
> Url: https://github.com/boostorg/core/pull/77  

Suggestion for #38.  
  
Added BOOST_TEST_WITH macro. Moved operation name of built-in predicates to a traits class.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-06-06 17:15:11 UTC  
> Url: https://github.com/boostorg/core/pull/77#issuecomment-640091682  

Thanks, I was thinking along the same lines. This will fail under C++03 but that's fixable and I can take care of it.

---

## Comment 2

> Username: breese  
> Created_at: 2020-06-06 17:17:55 UTC  
> Url: https://github.com/boostorg/core/pull/77#issuecomment-640092057  

Please do

---
