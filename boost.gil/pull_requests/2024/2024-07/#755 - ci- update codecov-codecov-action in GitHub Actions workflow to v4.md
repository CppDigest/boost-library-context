# #755 ci: update codecov/codecov-action in GitHub Actions workflow to v4 [Merged]

> Username: striezel  
> Created at: 2024-07-23 13:11:13 UTC  
> Updated at: 2024-07-23 23:18:24 UTC  
> Merged at: 2024-07-23 13:57:16 UTC  
> Closed at: 2024-07-23 13:57:16 UTC  
> Url: https://github.com/boostorg/gil/pull/755  

### Description  
  
This PR updates [`codecov/codecov-action`](https://github.com/codecov/codecov-action) to v4, it's current version.  
  
### References  
  
* It get's rid of the warning in CI due to outdated Node versions used by the older versions of the actions, e.g. as seen here: https://github.com/boostorg/gil/actions/runs/10054788474  
  
  > The following actions uses node12 which is deprecated and will be forced to run on node16: codecov/codecov-action@v1.2.1. For more info: https://github.blog/changelog/2023-06-13-github-actions-all-actions-will-run-on-node16-instead-of-node12-by-default/  
  
  Version 4 of the action [uses Node 20](https://github.com/codecov/codecov-action/blob/e28ff129e5465c2c0dcc6f003fc735cb6ae0c673/action.yml#L120).  
  
* As far as I understand, version 1.x of the action has been deprecated: https://about.codecov.io/blog/codecov-uploader-deprecation-plan/  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-23 13:22:19 UTC  
> Url: https://github.com/boostorg/gil/pull/755#issuecomment-2245241474  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/755?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 82.12%. Comparing base [(`322c4e2`)](https://app.codecov.io/gh/boostorg/gil/commit/322c4e2e191458383db0f2873dd3301f05a7d137?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1f88d18`)](https://app.codecov.io/gh/boostorg/gil/commit/1f88d1816d42ac763c7dcd2e218c7ad36d3a5e9f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 8 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #755   +/-   ##  
========================================  
  Coverage    82.12%   82.12%             
========================================  
  Files          117      117             
  Lines         5355     5355             
========================================  
  Hits          4398     4398             
  Misses         957      957             
```  
  
</details>

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2024-07-23 13:57:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/755#pullrequestreview-2194075418  

Thanks!

---
