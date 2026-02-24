# #27 Fix invalid XHTML markup in docs [Merged]

> Username: jwakely  
> Created at: 2020-08-05 10:31:56 UTC  
> Updated at: 2022-03-31 10:51:08 UTC  
> Merged at: 2021-11-09 17:44:28 UTC  
> Closed at: 2021-11-09 17:44:28 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/27  

The reStructuredText files insert `<hr>` HTML elements, which are not  
valid in XHTML.  
  
The doc/tutorial_example.html file contains unquoted attributes and  
unclosed `<meta>` and `<link>` tags (although since that file is included  
into another document, it probably shouldn't have `<html>`, `<head>` and  
`<body>` elements at all).

---

## Comment 1

> Username: jwakely  
> Created_at: 2020-08-05 10:32:25 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/27#issuecomment-669116896  

If you're going to use XHTML doctypes then you should use valid XHTML.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-08-05 11:45:44 UTC  
> Updated_at: 2020-08-05 12:29:34 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/27#issuecomment-669146530  

# [Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/27?src=pr&el=h1) Report  
> Merging [#27](https://codecov.io/gh/boostorg/ptr_container/pull/27?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ptr_container/commit/0d547c6e6e4819ab47ecd2b0f78c15d55816fa23&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ptr_container/pull/27/graphs/tree.svg?width=650&height=150&src=pr&token=D5x8WKDJ88)](https://codecov.io/gh/boostorg/ptr_container/pull/27?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #27   +/-   ##  
========================================  
  Coverage    63.97%   63.97%             
========================================  
  Files           35       35             
  Lines         1471     1471             
  Branches       548      548             
========================================  
  Hits           941      941             
  Misses          34       34             
  Partials       496      496             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/27?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/27?src=pr&el=footer). Last update [0d547c6...adc94a8](https://codecov.io/gh/boostorg/ptr_container/pull/27?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: mclow  
> Created_at: 2020-08-05 15:55:54 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/27#issuecomment-669275317  

This looks fine to me.

---

## Comment 4

> Username: jwakely  
> Created_at: 2021-11-09 17:27:41 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/27#issuecomment-964371138  

Ping

---

## Comment 5

> Username: jwakely  
> Created_at: 2021-11-09 18:18:14 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/27#issuecomment-964412068  

Thanks, Marshall!

---

## Comment 6

> Username: jwakely  
> Created_at: 2022-03-31 10:51:08 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/27#issuecomment-1084413713  

Could this please get merged to master for the next release?

---
