# #93 Format code by clang-format [Closed]

> Username: coder3101  
> Created at: 2020-07-13 06:38:13 UTC  
> Updated at: 2021-05-24 18:52:23 UTC  
> Closed at: 2021-05-24 18:52:23 UTC  
> Url: https://github.com/boostorg/ublas/pull/93  

This commit formats the code by `clang-tidy` according to the styles as mentioned in the `.clang-format` files in the root of the repository. The following locations have been formatted:  
  
- `examples/tensor/*.cpp`  
- `test/tensor/*.cpp`  
- `include/boost/numeric/ublas/tensor/*.hpp`  
- `include/boost/numeric/ublas/tensor/*/*.hpp`  
  
The please review the styles and if any change is required, I will reupdate the files with the new format.

---

## Comment 1

> Username: coder3101  
> Created_at: 2020-07-13 06:46:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/93#issuecomment-657389961  

CI failed because Github actions are down. See https://www.githubstatus.com/

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-07-13 08:53:35 UTC  
> Updated_at: 2020-07-24 22:26:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/93#issuecomment-657419543  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/93?src=pr&el=h1) Report  
> Merging [#93](https://codecov.io/gh/boostorg/ublas/pull/93?src=pr&el=desc) into [features/tensor_redesign](https://codecov.io/gh/boostorg/ublas/commit/6d2177f3420f69d6a1aaee20aceb9d0854568947&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/93/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/93?src=pr&el=tree)  
  
```diff  
@@                    Coverage Diff                    @@  
##           features/tensor_redesign      #93   +/-   ##  
=========================================================  
  Coverage                     94.16%   94.16%             
=========================================================  
  Files                            19       19             
  Lines                          1166     1166             
=========================================================  
  Hits                           1098     1098             
  Misses                           68       68             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/93?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/93?src=pr&el=footer). Last update [6d2177f...d296b12](https://codecov.io/gh/boostorg/ublas/pull/93?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: coder3101  
> Created_at: 2020-07-13 10:37:02 UTC  
> Url: https://github.com/boostorg/ublas/pull/93#issuecomment-657480858  

@bassoy and @amitsingh19975   
  
You can check the code formatting. Finally all CI tests are passing.

---

## Review 4 [Commented]

> Username: coder3101  
> Created_at: 2020-07-14 10:09:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/93#pullrequestreview-447971012  

📁 .github/workflows/code_format.yml

```diff
  38 |+       run: |
  39 |+         git remote add token https://${GITHUB_ACTOR}:${{ secrets.GITHUB_TOKEN }}@github.com/${GITHUB_REPOSITORY}.git
  40 |+         git push token HEAD:${GITHUB_REF} --force-with-lease=${GITHUB_REF}:${GITHUB_SHA}
```

> Username: coder3101  
> Created_at: 2020-07-14 10:09:33 UTC  
> Updated_at: 2020-07-24 22:18:42 UTC  
> Url: https://github.com/boostorg/ublas/pull/93#discussion_r454248945  

`--force-with-lease` makes sure that forced updates do not overwrite code of other contributors.


---

## Comment 5

> Username: coder3101  
> Created_at: 2020-07-14 10:13:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/93#issuecomment-658096660  

@bassoy I have added the auto clang-format feature. Whenever push occurs in our repository, the GitHub action will amend the last commit and format the code force update the repository with the lease.

---

## Comment 6

> Username: bassoy  
> Created_at: 2020-07-18 08:57:42 UTC  
> Url: https://github.com/boostorg/ublas/pull/93#issuecomment-660452447  

Thanks, looks good to me.

---

## Review 7 [Approved]

> Username: amitsingh19975  
> Created_at: 2020-07-18 09:31:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/93#pullrequestreview-451044887  

I love this request. It will improve the coding style.

---
