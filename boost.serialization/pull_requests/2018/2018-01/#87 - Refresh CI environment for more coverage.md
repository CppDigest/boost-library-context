# #87 Refresh CI environment for more coverage [Closed]

> Username: jeking3  
> Created at: 2018-01-20 05:15:06 UTC  
> Updated at: 2018-11-01 14:34:11 UTC  
> Closed at: 2018-11-01 14:34:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/87  

After adding ubsan builds to Datetime and Uuid, there were some issues in serialization.   This pull request adds a ubsan build job to serialization as an allowed failure so that issues can be uncovered.

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-04-30 20:36:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/87#issuecomment-385520771  

I'm OK with this. But it's not passing CI tests.  I'm guessing this is due to unrelated issues.  So for the moment, I'm going to let this sit a little bit until the CI starts to pass with all the other changes.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-05-01 15:27:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/87#issuecomment-385699706  

I will update this PR with a small fix, hopefully tonight; see:  
https://github.com/boostorg/format/commit/76293b18b1734c61ff6ada2dc3065ca4d52c5636

---

## Comment 3

> Username: robertramey  
> Created_at: 2018-05-17 20:36:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/87#issuecomment-390002807  

I would like to merge this and close it.  But I notice that there are some failures pending. What should I do with this?

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-05-18 01:41:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/87#issuecomment-390068380  

On the ubsan build job I just pushed a fix for the issue there.  I still expect it to fail, but I have marked ubsan and valgrind as non-fatal CI errors for you (although I think they should be looked at and there are open issues for them).  
  
The clang dynamic linux job seems like a legitimite issue however, segfault.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-11-01 13:21:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/87#issuecomment-435038830  

@robertramey if you have a chance to log into AppVeyor and cancel builds 191, 192 it will same some time (about 6 hours).

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-11-01 14:34:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/87#issuecomment-435059416  

Okay, I'll do it on my fork then.  I'll get back to you with defects identified.

---
