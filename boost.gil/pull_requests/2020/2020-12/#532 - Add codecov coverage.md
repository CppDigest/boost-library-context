# #532 Add codecov coverage [Merged]

> Username: codejaeger  
> Created at: 2020-12-29 18:14:54 UTC  
> Updated at: 2021-01-04 18:54:02 UTC  
> Merged at: 2021-01-04 18:47:02 UTC  
> Closed at: 2021-01-04 18:47:02 UTC  
> Url: https://github.com/boostorg/gil/pull/532  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
This PR adds code coverage using [Codecov](https://about.codecov.io/) as mentioned in #420 . Currently being generated using the Travis CI pipeline.  
  
### Demo  
  
![codecov](https://codecov.io/gh/codejaeger/gil/branch/codecov/graph/badge.svg)  
  
![coverage](https://user-images.githubusercontent.com/32168969/103351607-c346a180-4ac9-11eb-9a41-b655f0dd5f19.png)  
  
### References  
  
* https://docs.codecov.io/docs  
* https://docs.codecov.io/docs  
* https://github.com/boostorg/beast  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-12-30 13:55:26 UTC  
> Updated_at: 2020-12-30 14:15:20 UTC  
> Url: https://github.com/boostorg/gil/pull/532#issuecomment-752630326  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/532?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@df8068f`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/gil/pull/532/graphs/tree.svg?width=650&height=150&src=pr&token=rbI5NTc0vX)](https://codecov.io/gh/boostorg/gil/pull/532?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop     #532   +/-   ##  
==========================================  
  Coverage           ?   77.81%             
==========================================  
  Files              ?      110             
  Lines              ?     4367             
  Branches           ?        0             
==========================================  
  Hits               ?     3398             
  Misses             ?      969             
  Partials           ?        0             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/gil/pull/532?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/gil/pull/532?src=pr&el=footer). Last update [df8068f...f9a9665](https://codecov.io/gh/boostorg/gil/pull/532?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2021-01-04 18:46:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/532#pullrequestreview-561255531  

LGTM, Thanks!

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-01-04 18:46:47 UTC  
> Url: https://github.com/boostorg/gil/pull/532#issuecomment-754147821  

In near future, I'm hoping to migrate to GitHub Actions, then this script should be re-usable there too.

---
