# #617 Fix C++20 incompatibilities in IO extensions [Merged]

> Username: fmorgner  
> Created at: 2021-06-11 09:34:30 UTC  
> Updated at: 2021-10-31 15:03:16 UTC  
> Merged at: 2021-10-05 07:31:01 UTC  
> Closed at: 2021-10-05 07:31:02 UTC  
> Url: https://github.com/boostorg/gil/pull/617  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Replace the *simple-template-id*s used in the IO extension tags as constructor names for specializations of `image_read_info` and `image_read_settings` with the associated plain class names.  
  
### References  
  
resolves #616   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2021-06-11 12:00:10 UTC  
> Url: https://github.com/boostorg/gil/pull/617#issuecomment-859530772  

LGTM and thanks for the fix! I remember asking myself what were this `image_read_info< xxx_tag >()` ctor about but they were armless at the time.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-06-11 12:06:07 UTC  
> Url: https://github.com/boostorg/gil/pull/617#issuecomment-859534054  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/617?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#617](https://codecov.io/gh/boostorg/gil/pull/617?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (effd078) into [develop](https://codecov.io/gh/boostorg/gil/commit/7dd61209a81695600d773f481bb8b22b0f74a30a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7dd6120) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #617   +/-   ##  
========================================  
  Coverage    78.72%   78.72%             
========================================  
  Files          118      118             
  Lines         5034     5034             
========================================  
  Hits          3963     3963             
  Misses        1071     1071             
```

---

## Comment 3

> Username: fmorgner  
> Created_at: 2021-06-11 13:27:34 UTC  
> Url: https://github.com/boostorg/gil/pull/617#issuecomment-859581622  

I believe the build issues in `CI / windows (msvc-14.2, 14,17,latest, 32,64, windows-2019)` are the same as on the `develop` branch, while `CI / posix (clang, clang++-9, 11,14,17,2a, ubuntu-20.04)` seems to have an issue with a missing clang-9 installation.

---

## Comment 4

> Username: sdebionne  
> Created_at: 2021-06-15 19:39:13 UTC  
> Url: https://github.com/boostorg/gil/pull/617#issuecomment-861779608  

From what I read on slack, the GHA guys have fun changing the docker images without notice. I am not a CI guru, @mloskot can this be easily fixed? Apparently `develop` currently fails with the same error with clang-8/clang-9.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2021-10-04 12:54:59 UTC  
> Url: https://github.com/boostorg/gil/pull/617#issuecomment-933454502  

@mloskot I'd like to merge this one, any objection?

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2021-10-04 15:57:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/617#pullrequestreview-770486046  

LGTM. Thanks!

---

## Comment 7

> Username: mloskot  
> Created_at: 2021-10-04 15:59:20 UTC  
> Url: https://github.com/boostorg/gil/pull/617#issuecomment-933625517  

@sdebionne Please, do. Thank you for nudging me about it.  
  
Sorry, I have no having any solution for the CI issues right now. I should be able to look at that next month.

---

## Comment 8

> Username: sdebionne  
> Created_at: 2021-10-05 07:30:47 UTC  
> Url: https://github.com/boostorg/gil/pull/617#issuecomment-934143326  

@mloskot No worries and unfortunately I don't have time to look at the CI neither... but this should be safe to merge.

---
