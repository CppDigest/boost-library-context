# #3 Feature/ignore unused [Merged]

> Username: awulkiew  
> Created at: 2014-06-02 14:19:10 UTC  
> Updated at: 2014-06-02 19:25:38 UTC  
> Merged at: 2014-06-02 19:17:59 UTC  
> Closed at: 2014-06-02 19:17:59 UTC  
> Url: https://github.com/boostorg/core/pull/3  

boost::ignore_unused_variable_warning() function is defined in ConceptCheck but also in the internals of other libraries (GIL, Interval, Test, uBlas, Unordered) probably to not include the whole concept_check.hpp. It would be convenient to have such tool ready-to-use in all libraries in a lightweight form, not introducing any additional dependencies. Newly introduced Boost.Core is a good place where it could reside.  
  
Followed by the: https://github.com/boostorg/utility/pull/8  
Discussion: http://boost.2283326.n4.nabble.com/Standalone-boost-ignore-unused-variable-warning-td4662481.html

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-06-02 19:25:38 UTC  
> Url: https://github.com/boostorg/core/pull/3#issuecomment-44879829  

Merged. g++ 4.5 doesn't support -Wunused-local-typedefs though, which causes the test to fail.

---
