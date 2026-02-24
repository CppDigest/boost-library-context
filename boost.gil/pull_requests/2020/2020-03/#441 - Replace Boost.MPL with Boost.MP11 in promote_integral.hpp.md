# #441 Replace Boost.MPL with Boost.MP11 in promote_integral.hpp [Merged]

> Username: codejaeger  
> Created at: 2020-03-05 10:44:33 UTC  
> Updated at: 2020-03-10 18:47:19 UTC  
> Merged at: 2020-03-10 18:46:05 UTC  
> Closed at: 2020-03-10 18:46:05 UTC  
> Url: https://github.com/boostorg/gil/pull/441  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
This PR is to replace all occurrences of **Boost.mpl's** meta functions with equivalents from **Boost.mp11** as mentioned in #280. I have tried to make **minimal** number of changes to the source code while using only functionality from Boost.mp11 . I have also tried using other ways to do the replacement using mp_find_if or mp_cond from the mp11 docs but this PR proposes using mp_rest to recurse over the type list.  
  
### References  
  
https://www.boost.org/doc/libs/develop/libs/mp11/doc/html/mp11.html#mp_restl  
https://www.boost.org/doc/libs/develop/libs/mp11/doc/html/mp11.html#mp_find_ifl_p  
https://www.boost.org/doc/libs/develop/libs/mp11/doc/html/mp11.html#mp_condc_t_r  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] <del>Add test case(s)</del> Check existing tests pass.  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-03-08 23:39:43 UTC  
> Url: https://github.com/boostorg/gil/pull/441#issuecomment-596266739  

@codejaeger Is this ready to review?

---

## Comment 2

> Username: codejaeger  
> Created_at: 2020-03-09 06:16:19 UTC  
> Url: https://github.com/boostorg/gil/pull/441#issuecomment-596349310  

@mloskot To give an overview of what changes I made and what not:  
**Changes** -  
1. Replaced mpl meta functions with mp11 equivalents   
2. Updated license information (although not entirely sure if I meet the originality threshold mentioned over [here](https://www.boost.org/users/license.html))  
  
**Did not change** -   
1. Core logic of the code  
2. Comments since they seemed mp(11/l) independent  
  
I think it is ready for review. Thanks.

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-09 21:07:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/441#pullrequestreview-371524900  

@codejaeger This looks good. Could you just address parameter renaming?

📁 include/boost/gil/promote_integral.hpp

```diff
  50 |-     typename Iterator,
  51 |-     typename EndIterator,
  49 |+     typename type_list,
```

> Username: mloskot  
> Created_at: 2020-03-09 20:59:26 UTC  
> Updated_at: 2020-03-10 04:57:26 UTC  
> Url: https://github.com/boostorg/gil/pull/441#discussion_r389958331  

Could you rename `type_list` to `IntegralTypes`?  
The template parameters are CamelCase-ed.


---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2020-03-10 08:07:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/441#pullrequestreview-371742055  

Thank you!  
  
I will merge this as soon as all CI jobs complete.

---
