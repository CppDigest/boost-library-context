# #599 refactor: Switch to trailing return types in core [Merged]

> Username: mloskot  
> Created at: 2021-04-25 18:04:44 UTC  
> Updated at: 2022-06-27 16:02:08 UTC  
> Merged at: 2022-06-27 16:01:49 UTC  
> Closed at: 2022-06-27 16:01:49 UTC  
> Url: https://github.com/boostorg/gil/pull/599  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
- Trailing return types everywhere  
- Optionally, return type deduction where sensible (simple and short functions)  
- Apply `//` hack for `clang-format`, see https://github.com/boostorg/gil/pull/596#issuecomment-833941725  
- Extensions, especially the large IO are skipped, left for future  
  
### References  
  
- This is related to introduction of common .clang-format, see https://github.com/boostorg/gil/pull/596#issuecomment-822681523  
  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [ ] Review and approve - a thorough review is not required

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-25 21:05:40 UTC  
> Updated_at: 2022-06-27 11:59:24 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826389861  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/599?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#599](https://codecov.io/gh/boostorg/gil/pull/599?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (390e1cc) into [develop](https://codecov.io/gh/boostorg/gil/commit/95679b6280b4cc8a20c208fb0008c4579a1a8070?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (95679b6) will **increase** coverage by `0.11%`.  
> The diff coverage is `99.56%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #599      +/-   ##  
===========================================  
+ Coverage    80.75%   80.86%   +0.11%       
===========================================  
  Files          116      116                
  Lines         5086     5117      +31       
===========================================  
+ Hits          4107     4138      +31       
  Misses         979      979                
```

---

## Comment 2

> Username: lpranam  
> Created_at: 2021-04-26 05:53:41 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826525747  

btw how did you find and decide which function should change to trailing return type? did you do it manually or some script?

---

## Comment 3

> Username: lpranam  
> Created_at: 2021-04-26 07:04:10 UTC  
> Updated_at: 2021-04-26 07:05:02 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826565331  

I think we should make a new branch for all the refactoring for now, freeze merge in develop for a week or so. put all the refactoring change in this branch and then just squash all those and merge in develop. what are your thoughts?  
  
would love to have a single commit for all these changes(just my preference)

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-04-26 07:56:57 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826601761  

> did you do it manually or some script?  
  
Manually  
  
> would love to have a single commit for all these changes(just my preference)  
  
Yes, there certainly will be a single commit. We always do "Squash and merge".  
The multiple commits is just temporarily, for my convenience.  
  
So, I don't think any freezing is necessary. I will plough on with the PR over next days, syncing it with the `develop`, then we squash `ml/refactor-with-trailing-return` and merge.   
Do you see any problem with that approach?  
  
The branch can be updated by anyone, by the way.

---

## Comment 5

> Username: lpranam  
> Created_at: 2021-04-26 08:24:11 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826622199  

I was hoping for trailing return + refactor in one commit. changing to trailing return type is a kinda reflector too that's why. Otherwise, we can have these separate I don't mind either way.

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-04-26 08:49:16 UTC  
> Updated_at: 2021-04-26 09:01:04 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826641722  

> I was hoping for trailing return + refactor in one commit. changing to trailing return type is a kinda reflector too that's why.   
  
I'm sorry but I'm lost.  
  
In https://github.com/boostorg/gil/pull/599#issuecomment-826601761 I explained that this PR #599 will eventually become a single commit PR. What else do you mean to suggest?  
  
UPDATE: Commits squashed ;)

---

## Comment 7

> Username: lpranam  
> Created_at: 2021-04-26 11:35:44 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826764021  

I mean this trailing return type changes + the Big reformat as one commit

---

## Comment 8

> Username: mloskot  
> Created_at: 2021-04-26 11:45:16 UTC  
> Updated_at: 2021-04-26 11:45:29 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826769682  

If you don't mind, I'd prefer to keep them separate. They both fall into the refactoring category, but the C++ syntax change is different from style change.

---

## Comment 9

> Username: lpranam  
> Created_at: 2021-04-26 12:06:32 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-826782206  

yeah makes sense, let keep them separate.

---

## Review 10 [Commented]

> Username: mloskot  
> Created_at: 2021-04-29 16:50:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/599#pullrequestreview-648437647  

📁 include/boost/gil/bit_aligned_pixel_iterator.hpp

```diff
  75 |     friend class boost::iterator_core_access;
  76 |-     reference dereference()     const { return NonAlignedPixelReference(_bit_range); }
  76 |+     auto dereference() const -> reference { return NonAlignedPixelReference(_bit_range); }
```

> Username: mloskot  
> Created_at: 2021-04-29 16:50:30 UTC  
> Updated_at: 2021-05-06 21:04:02 UTC  
> Url: https://github.com/boostorg/gil/pull/599#discussion_r623226585  

Test non-review single comment


---

## Review 11 [Commented]

> Username: mloskot  
> Created_at: 2021-04-29 16:51:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/599#pullrequestreview-648438115  

📁 include/boost/gil/bit_aligned_pixel_iterator.hpp

```diff
  80 | 
  81 |-     difference_type distance_to(const bit_aligned_pixel_iterator& it) const { return _bit_range.bit_distance_to(it._bit_range) / bit_size; }
  81 |+     auto distance_to(bit_aligned_pixel_iterator const& it) const -> difference_type { return _bit_range.bit_distance_to(it._bit_range) / bit_size; }
```

> Username: mloskot  
> Created_at: 2021-04-29 16:50:59 UTC  
> Updated_at: 2021-05-06 21:04:02 UTC  
> Url: https://github.com/boostorg/gil/pull/599#discussion_r623226951  

Test review comment


---

## Comment 12

> Username: mloskot  
> Created_at: 2021-05-06 22:02:49 UTC  
> Updated_at: 2021-05-06 22:03:08 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-833900325  

@lpranam This is finished and if the CI-s get green, then I'm going to merge it. I don't think there is need to actually run the line by line review, unless you think otherwise.

---

## Comment 13

> Username: lpranam  
> Created_at: 2021-05-07 02:08:56 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-834005790  

@mloskot I am happy to not review this line by line 😉

---

## Comment 14

> Username: mloskot  
> Created_at: 2021-05-07 08:09:55 UTC  
> Updated_at: 2021-05-07 08:11:08 UTC  
> Url: https://github.com/boostorg/gil/pull/599#issuecomment-834158309  

@lpranam   
> I am happy to not review this line by line 😉  
  
https://www.youtube.com/watch?v=EN90RWb9f9M&t=23s  
  
I just wanted to make it clear in case you wonder what I may expect to happen about it here 😛

---
