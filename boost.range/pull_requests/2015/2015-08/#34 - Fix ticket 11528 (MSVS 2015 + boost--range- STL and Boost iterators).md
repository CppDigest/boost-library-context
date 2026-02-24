# #34 Fix ticket 11528 (MSVS 2015 + boost::range: STL and Boost iterators) [Merged]

> Username: morinmorin  
> Created at: 2015-08-20 11:31:53 UTC  
> Updated at: 2019-04-29 14:03:54 UTC  
> Merged at: 2015-09-07 06:44:35 UTC  
> Closed at: 2015-09-07 06:44:35 UTC  
> Url: https://github.com/boostorg/range/pull/34  



---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-16 04:15:48 UTC  
> Url: https://github.com/boostorg/range/pull/34#issuecomment-430095171  

This pull request has introduced an unfortunate circular dependency between Algorithm and Range. It would be good if we can fix this.

---

## Comment 2

> Username: morinmorin  
> Created_at: 2018-10-20 13:15:38 UTC  
> Url: https://github.com/boostorg/range/pull/34#issuecomment-431580843  

> This pull request has introduced an unfortunate circular dependency between Algorithm and Range.   
  
Why does Algorithm depend on Range? StringAlgo is the culprit?  
  
> It would be good if we can fix this.  
  
I think implementation of `min_element/max_element` would be easy (and we can mark it as `BOOST_CXX14_CONSTEXPR` as an enhancement), but this introduces duplication and divergence of the same function between Range and Algorithm...

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-10-20 14:38:44 UTC  
> Url: https://github.com/boostorg/range/pull/34#issuecomment-431587407  

> Why does Algorithm depend on Range?  
  
https://pdimov.github.io/boostdep-report/develop/algorithm.html#range

---

## Comment 4

> Username: morinmorin  
> Created_at: 2019-04-29 14:03:54 UTC  
> Url: https://github.com/boostorg/range/pull/34#issuecomment-487592539  

For the record, the dependency on Boost.Algorithm is now removed by #91.  
  
@pdimov Sorry for not fixing the dependency issue. I made a fix, but forgot to submit PR.

---
