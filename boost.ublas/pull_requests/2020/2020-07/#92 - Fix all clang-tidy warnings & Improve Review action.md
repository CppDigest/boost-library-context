# #92 Fix all clang-tidy warnings & Improve Review action [Merged]

> Username: coder3101  
> Created at: 2020-07-12 07:41:15 UTC  
> Updated at: 2020-07-12 16:33:59 UTC  
> Merged at: 2020-07-12 16:16:47 UTC  
> Closed at: 2020-07-12 16:16:48 UTC  
> Url: https://github.com/boostorg/ublas/pull/92  

This PR fixes all clang-tidy warnings so that all clang-tidy all clang-tidy checks will pass.  
  
It basically has the following changes:  
1. Remove `return after else` warnings.  
2. Expand macros in `operator_arithematics.hpp`  
3. Suppress false warnings from `assert()` in the codebase.  
4. Suppress `bug-exception-escape` for tensor examples.

---

## Comment 1

> Username: coder3101  
> Created_at: 2020-07-12 08:27:41 UTC  
> Updated_at: 2020-07-12 08:28:49 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657191045  

Clang-tidy review failed because `GITHUB_TOKEN` is limited for repository forks to only read and not write.  
  
From Github docs:  
> Creating the comment will not work when someone creates a pull request from a fork. The GITHUB_TOKEN secret is still passed but has only read permissions, it cannot create or update anything. If that was not the case, anyone could create a pull request changing the code of the action script to do something malicious with your repository.  
  
In short, review cannot be performed for PR that originate from forks.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-07-12 08:45:05 UTC  
> Updated_at: 2020-07-12 10:00:06 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657192618  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/92?src=pr&el=h1) Report  
> Merging [#92](https://codecov.io/gh/boostorg/ublas/pull/92?src=pr&el=desc) into [features/tensor_redesign](https://codecov.io/gh/boostorg/ublas/commit/5a8038f8849f05590ef8ea260a4932df47e91599&el=desc) will **increase** coverage by `0.05%`.  
> The diff coverage is `85.71%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/92/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/92?src=pr&el=tree)  
  
```diff  
@@                     Coverage Diff                      @@  
##           features/tensor_redesign      #92      +/-   ##  
============================================================  
+ Coverage                     70.39%   70.44%   +0.05%       
============================================================  
  Files                            19       19                
  Lines                          1162     1164       +2       
  Branches                        471      471                
============================================================  
+ Hits                            818      820       +2       
  Misses                           67       67                
  Partials                        277      277                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/92?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/ublas/tensor/functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9mdW5jdGlvbnMuaHBw) | `51.70% <ø> (ø)` | |  
| [...lude/boost/numeric/ublas/tensor/multiplication.hpp](https://codecov.io/gh/boostorg/ublas/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aXBsaWNhdGlvbi5ocHA=) | `65.00% <ø> (ø)` | |  
| [.../numeric/ublas/tensor/detail/extents\_functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9kZXRhaWwvZXh0ZW50c19mdW5jdGlvbnMuaHBw) | `70.90% <66.66%> (ø)` | |  
| [...oost/numeric/ublas/tensor/operators\_arithmetic.hpp](https://codecov.io/gh/boostorg/ublas/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9vcGVyYXRvcnNfYXJpdGhtZXRpYy5ocHA=) | `75.43% <100.00%> (+0.89%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/92?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/92?src=pr&el=footer). Last update [5a8038f...eebabc0](https://codecov.io/gh/boostorg/ublas/pull/92?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: amitsingh19975  
> Created_at: 2020-07-12 09:22:05 UTC  
> Updated_at: 2020-07-12 09:22:46 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657196144  

@coder3101  could you also create another pull request for `clang` formatting for the whole tensor directory after all the pull request is accepted so that afterward no one has to format everything and create a huge pull request and that is the reason I can not do the formatting with my current pull request it will be hard to reason about the pull request and ask about this to @bassoy  also.

---

## Review 4 [Approved]

> Username: amitsingh19975  
> Created_at: 2020-07-12 09:24:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/92#pullrequestreview-446861541  

This will reduce my work and thanks @coder3101 and help in passing `clang-tidy`. I'm approving this pull request.

---

## Comment 5

> Username: coder3101  
> Created_at: 2020-07-12 09:29:09 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657196917  

>   
>   
> @coder3101 could you also create another pull request for `clang` formatting for the whole tensor directory after all the pull request is accepted so that afterward no one has to format everything and create a huge pull request and that is the reason I can not do the formatting with my current pull request it will be hard to reason about the pull request and ask about this to @bassoy also.  
  
I was about to do the same.

---

## Comment 6

> Username: amitsingh19975  
> Created_at: 2020-07-12 09:30:37 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657197084  

> > @coder3101 could you also create another pull request for `clang` formatting for the whole tensor directory after all the pull request is accepted so that afterward no one has to format everything and create a huge pull request and that is the reason I can not do the formatting with my current pull request it will be hard to reason about the pull request and ask about this to @bassoy also.  
>   
> I was about to do the same.  
  
Thanks.

---

## Comment 7

> Username: coder3101  
> Created_at: 2020-07-12 09:47:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657198963  

I am done, as soon as @bassoy approves this PR, It can be merged.  
  
I have done some changes to the review actions, so Review will not only be performed for PR from forks. It is because Token to create issue comments are not propagated to forked repositories and hence script does not have permission to create comments on PR from forks.  
  
**The review will be performed only if PR originates from `boostorg/ublas` other branches.**

---

## Comment 8

> Username: bassoy  
> Created_at: 2020-07-12 16:16:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657243643  

> I am done, as soon as @bassoy approves this PR, It can be merged.  
>   
> I have done some changes to the review actions, so Review will not only be performed for PR from forks. It is because Token to create issue comments are not propagated to forked repositories and hence script does not have permission to create comments on PR from forks.  
>   
> **The review will be performed only if PR originates from `boostorg/ublas` other branches.*  
  
so clang-tidy will be hopefully performed hopefully anyways.

---

## Comment 9

> Username: coder3101  
> Created_at: 2020-07-12 16:33:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/92#issuecomment-657245959  

> so clang-tidy will be hopefully performed hopefully anyways.  
  
Clang-tidy checks will be performed everywhere, it's that clang tidy review comments will not be done for PR from forks.  
  
CI will fail if any PR has tidy issue, we can look at build logs for the issue and fix them.

---
