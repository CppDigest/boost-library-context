# #648 feat: Added for_each_pixel overload for any_image [Merged]

> Username: marco-langer  
> Created at: 2022-04-24 11:59:59 UTC  
> Updated at: 2022-04-30 21:57:30 UTC  
> Merged at: 2022-04-30 21:57:00 UTC  
> Closed at: 2022-04-30 21:57:01 UTC  
> Url: https://github.com/boostorg/gil/pull/648  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
Fixes #579   
  
### References  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: marco-langer  
> Created_at: 2022-04-24 13:51:06 UTC  
> Url: https://github.com/boostorg/gil/pull/648#issuecomment-1107845960  

The suggested solution in the issue does not work, because we cannot use generic lambdas in C++11.  
  
I've marked my PR as WIP, because I am not quite sure whether it is a good idea to take the address of the callable and store a pointer to it in the helper function object. I could also refactor it to take it by value and move it into the helper functor. Any options on this?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-04-25 10:29:03 UTC  
> Updated_at: 2022-04-29 17:56:41 UTC  
> Url: https://github.com/boostorg/gil/pull/648#issuecomment-1108388068  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/648?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#648](https://codecov.io/gh/boostorg/gil/pull/648?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fc88042) into [develop](https://codecov.io/gh/boostorg/gil/commit/caf92fa94ba4b5960b7431dd9c7e391400aa293d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (caf92fa) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #648      +/-   ##  
===========================================  
+ Coverage    78.77%   78.79%   +0.02%       
===========================================  
  Files          117      117                
  Lines         5031     5036       +5       
===========================================  
+ Hits          3963     3968       +5       
  Misses        1068     1068                
```

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-04-25 16:34:13 UTC  
> Updated_at: 2022-04-25 16:35:03 UTC  
> Url: https://github.com/boostorg/gil/pull/648#issuecomment-1108794198  

@marco-langer Thank you for the PR. Storing the functor directly, by value, is fine.

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-04-29 17:42:44 UTC  
> Url: https://github.com/boostorg/gil/pull/648#issuecomment-1113565611  

What's the status of this PR?  
FYI, I've just approved all CI jobs to run against this PR.

---

## Comment 5

> Username: marco-langer  
> Created_at: 2022-04-30 04:28:38 UTC  
> Url: https://github.com/boostorg/gil/pull/648#issuecomment-1113913036  

The PR is ready for review, if we are currently on C++11.  
  
However, yesterday I saw a comment of you saying that we are actually able to develop in C++14 already. If this is the case (the GIL github project description is not correct then), I would like to refactor this PR again to replace the functor and use a generic lambda to follow the suggestion in the linked issue.

---

## Comment 6

> Username: mloskot  
> Created_at: 2022-04-30 08:53:47 UTC  
> Url: https://github.com/boostorg/gil/pull/648#issuecomment-1113951641  

In commit https://github.com/boostorg/gil/commit/8b1c2d3ea4eb5bf0a1e7e093862962313dd6c2bb we announced possibility to switch over to C++14 in the `RELEASES.md`.  
We also have started with @sdebionne and @lpranam discussion about switch to C++17, see https://github.com/boostorg/gil/pull/526#issuecomment-715220681 and https://github.com/boostorg/gil/pull/526#issuecomment-718007963 and there probably are places too.  
  
There are lots of details to consider, but if you'd like to discuss pros and cons of the C++ upgrade in the context of GIL enhancements, please feel free and welcomed to [open discussion](https://github.com/boostorg/gil/discussions).

---

## Comment 7

> Username: marco-langer  
> Created_at: 2022-04-30 14:54:06 UTC  
> Url: https://github.com/boostorg/gil/pull/648#issuecomment-1114001511  

You are right, discussing further steps for a possible C++14/C++17 version upgrade sounds like a good idea.  
  
Let's keep this PR with C++11-style functors then and postpone a further modernization.

---

## Review 8 [Approved]

> Username: mloskot  
> Created_at: 2022-04-30 21:55:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/648#pullrequestreview-958522852  

LGTM. Thank you!

---
