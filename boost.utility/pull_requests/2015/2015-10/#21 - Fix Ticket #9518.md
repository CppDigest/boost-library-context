# #21 Fix Ticket #9518 [Closed]

> Username: gatlex  
> Created at: 2015-10-18 17:03:37 UTC  
> Updated at: 2020-06-17 20:35:41 UTC  
> Closed at: 2020-06-17 20:35:40 UTC  
> Url: https://github.com/boostorg/utility/pull/21  

This PR fixes [Ticket #9518](https://svn.boost.org/trac/boost/ticket/9518).  
  
The idea is to generalize the helper reverse_distance by a correction factor and use the search string's length in rfind. All other usages have string length 1 in this regard.  
  
A test detecting the Bug is added to the regression tests, see string_ref_test2.cpp.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-10-18 18:24:50 UTC  
> Url: https://github.com/boostorg/utility/pull/21#issuecomment-149036722  

Please, rebase the PR against develop.

---

## Comment 2

> Username: mclow  
> Created_at: 2015-10-19 00:13:01 UTC  
> Url: https://github.com/boostorg/utility/pull/21#issuecomment-149066202  

I like this change; I have a couple nits (i.e, that's not how I'd write it) - but they're minor.  
  
I would make the last parameter to `reverse_distance` default to 1, and then only pass it when you've got a string, and I would give it a better name than `correction`; something like `pattern_length` that has more meaning.  
  
But, as @Lastique has said, the most important thing that needs to change in this PR is that it is against master; and we don't take pull requests into master. We pull into develop, watch the test results, and then merge into master.

---

## Comment 3

> Username: gatlex  
> Created_at: 2015-10-19 06:01:37 UTC  
> Url: https://github.com/boostorg/utility/pull/21#issuecomment-149110275  

I just noticed that [this PR](https://github.com/boostorg/utility/pull/2) seems to fix the same bug. As I see it, it eliminates `reverse_distance` altogether using `std::distance`. I like that even more.  
  
But at least the new tests might be worth incorporating, however, so I'd split the test-part of the PR off to be merged independently. If you as a maintainer think it's worth it, I will rebase also the bugfix to `develop`.  
  
What do you think?

---

## Comment 4

> Username: mclow  
> Created_at: 2015-10-19 06:06:18 UTC  
> Url: https://github.com/boostorg/utility/pull/21#issuecomment-149110992  

I'll definitely take the tests; thanks.  Don't sweat rebasing stuff; I'll do it.

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-01-27 21:21:24 UTC  
> Url: https://github.com/boostorg/utility/pull/21#issuecomment-275777703  

Is this PR still relevant?

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-06-15 15:50:38 UTC  
> Url: https://github.com/boostorg/utility/pull/21#issuecomment-644217328  

Ping?

---

## Comment 7

> Username: gatlex  
> Created_at: 2020-06-17 20:35:40 UTC  
> Url: https://github.com/boostorg/utility/pull/21#issuecomment-645610273  

@Lastique: I think it's obsolete, because it was a fix for [Ticket #9518](https://svn.boost.org/trac/boost/ticket/9518) which seems to be fixed by some other PR. I'll close it.

---
