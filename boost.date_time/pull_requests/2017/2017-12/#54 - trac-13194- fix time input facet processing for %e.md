# #54 trac-13194: fix time input facet processing for %e [Merged]

> Username: jeking3  
> Created at: 2017-12-21 20:40:41 UTC  
> Updated at: 2017-12-23 19:29:23 UTC  
> Merged at: 2017-12-23 19:29:23 UTC  
> Closed at: 2017-12-23 19:29:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/54  

Patch comes from the posted patch in the Boost Trac issue:  
https://svn.boost.org/trac10/ticket/13194

---

## Review 1 [Commented]

> Username: mclow  
> Created_at: 2017-12-22 00:20:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/54#pullrequestreview-85216227  

📁 test/posix_time/testtime_input_facet.cpp

```diff
  45 | template<class temporal_type>
  46 |- bool failure_test(temporal_type component,
  46 |+ bool failure_test(temporal_type& component,
```

> Username: mclow  
> Created_at: 2017-12-22 00:20:36 UTC  
> Updated_at: 2017-12-22 13:02:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/54#discussion_r158407167  

Why change this to a &? and if you're changing it, why not change the other overload of `failure_test` as well?

> Username: jeking3  
> Created_at: 2017-12-22 01:42:32 UTC  
> Updated_at: 2017-12-22 13:02:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/54#discussion_r158415460  

Hi @mclow. I only needed a reference here so that I could leverage failure_test for code re-use, and call check("...", !failiure_test()) to prove something did not fail, did not set the failbit, AND store the value in component (hence the reference).


---

## Comment 2

> Username: jeking3  
> Created_at: 2017-12-22 13:01:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/54#issuecomment-353594182  

I'm going to add to the format flags documentation for %d and %e indicating when used for input the leading 0 or space are optional.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-12-22 13:03:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/54#issuecomment-353594530  

(The previous build before the xml file was change passed: https://travis-ci.org/boostorg/date_time/builds/319902995?utm_source=github_status&utm_medium=notification)

---

## Comment 4

> Username: eldiener  
> Created_at: 2017-12-22 13:25:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/54#issuecomment-353597994  

This looks good to me. I will wait awhile to merge for Marshall to respond if he wants to.

---
