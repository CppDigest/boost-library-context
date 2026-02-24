# #111 [circleci] Allow only master and develop branches for all build jobs [Merged]

> Username: mloskot  
> Created at: 2018-06-27 19:15:42 UTC  
> Updated at: 2018-06-27 20:15:13 UTC  
> Merged at: 2018-06-27 20:03:46 UTC  
> Closed at: 2018-06-27 20:03:46 UTC  
> Url: https://github.com/boostorg/gil/pull/111  

CircleCI 2.0 does not yet support branch filtering per workflow.  
  
-----  
  
This only rationalizes CircleCI setup to avoid unnecessary use of resources for non-builds (eg. for `gh-pages` branch).  
  
This should also be merged into `master` for best effect.

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-06-27 19:18:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/111#pullrequestreview-132572727  

Not knowing CircleCI (or its config format), I can only trust that the changes are syntactically and grammatically valid. But I fully support the idea... :-)

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-06-27 19:31:02 UTC  
> Updated_at: 2018-06-27 19:31:32 UTC  
> Url: https://github.com/boostorg/gil/pull/111#issuecomment-400802063  

@stefanseefeld CircleCI uses YAML or at least that is what it claims to be.  
  
Unfortunately, I don't have access to docker-enabled Linux machine currently, so I am not able to [run the local CircleCI to validate it](as https://circleci.com/docs/2.0/local-cli/#validating-20-yaml-syntax)  
```  
circleci config validate -c .circleci/config.yml  
```  
  
But, before opening the PR, I checked the `config.yml` in https://jsonformatter.org/yaml-validator  
  
Other than that, I can cross my fingers :)

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-06-27 19:33:28 UTC  
> Url: https://github.com/boostorg/gil/pull/111#issuecomment-400802789  

@stefanseefeld Let me know if you want to do it or I should unblock the merging, merge this into `develop` and re-block the merging.   
  
I shall leave merging back to `master` to you, no?

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-06-27 19:50:01 UTC  
> Url: https://github.com/boostorg/gil/pull/111#issuecomment-400807355  

Either is fine with me. The frequency by which `master` receives pushes should be extremely low, so I'm not worried about that. (I expect to do perhaps one more before the release - the current deadline is only for "major changes", not changes in general.)  
But for `develop`, I actually don't mind if we reduce the threshold to a single reviewer, just not  to get blocked. As long as we don't take PRs lightly, I think a single review should suffice. :-)

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-06-27 20:02:13 UTC  
> Url: https://github.com/boostorg/gil/pull/111#issuecomment-400810758  

Sure, I'll merge to both and make the adjustments.

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-06-27 20:15:13 UTC  
> Url: https://github.com/boostorg/gil/pull/111#issuecomment-400814387  

Merged into `develop` and `master`

---
