# #529 Run headers tests only on demand [Merged]

> Username: mloskot  
> Created at: 2018-11-21 05:08:56 UTC  
> Updated at: 2018-12-10 09:41:18 UTC  
> Merged at: 2018-11-21 21:09:01 UTC  
> Closed at: 2018-11-21 21:09:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/529  

Disable headers tests on CI services and during regression tests.  
Refines #525

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-11-21 13:24:10 UTC  
> Updated_at: 2018-11-21 13:25:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/529#issuecomment-440660638  

What do you think about simply removing this script and relying on manually running tests in `self_contained_headers` directory? This way one wouldn't have to create any environment variable so the command would be shorter.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-11-21 15:06:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/529#issuecomment-440696464  

I planned to add a CI build job on Travis CI or AppVeyor Linux where I would define `TEST_HEADERS`.  
I can equally run `cd self_contained_headers  && b2`.  
  
So, no objection to remove it.  
  
I will update this PR.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-11-21 16:00:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/529#issuecomment-440718470  

@awulkiew I see you have added 1.69 milestone. Since you aim to merge this into `master` too, I will leave merge of this to you.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-11-21 21:08:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/529#issuecomment-440808745  

Thanks.

---
