# #567 ci: Add Codecov badge and codecov.yml file [Merged]

> Username: avinal  
> Created at: 2021-02-28 09:58:56 UTC  
> Updated at: 2021-03-01 15:03:39 UTC  
> Merged at: 2021-03-01 10:46:27 UTC  
> Closed at: 2021-03-01 10:46:27 UTC  
> Url: https://github.com/boostorg/gil/pull/567  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
- Adds Codecov badge to README.md  
- Corrects appveyor badge  
  
### References  
  
Fixes #566  
Related PR #564  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-02-28 19:57:42 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/567#pullrequestreview-600257285  

The `branches` needs confirmation

📁 codecov.yml

```diff
   4 |+   branches:
   5 |+     - master
   6 |+     - develop
```

> Username: mloskot  
> Created_at: 2021-02-28 13:14:28 UTC  
> Updated_at: 2021-02-28 19:57:42 UTC  
> Url: https://github.com/boostorg/gil/pull/567#discussion_r584293794  

Are we sure this does not disable the coverage analysis for PRs?  
We want reports via comments to PRs, don't we?

> Username: avinal  
> Created_at: 2021-03-01 05:12:32 UTC  
> Updated_at: 2021-03-01 05:14:10 UTC  
> Url: https://github.com/boostorg/gil/pull/567#discussion_r584449999  

I don't think it will disable comments for PR, this controls which branches should be considered while commenting, i.e. if the head of the pull request is master or develop, then only comments will work. https://docs.codecov.io/docs/pull-request-comments#branches  
  
I think we can remove this, as PR are mostly headed to the develop branch. Please let me know if we should remove it.

> Username: mloskot  
> Created_at: 2021-03-01 10:44:23 UTC  
> Url: https://github.com/boostorg/gil/pull/567#discussion_r584609808  

Okey, let's keep it as it is and we will see how it works

---

📁 codecov.yml

```diff
   1 |+ comment:
   2 |+   layout: "diff"
```

> Username: mloskot  
> Created_at: 2021-02-28 19:57:11 UTC  
> Updated_at: 2021-02-28 19:57:43 UTC  
> Url: https://github.com/boostorg/gil/pull/567#discussion_r584351770  

👍 I think we don't need the heatmap (as displayed here https://github.com/boostorg/gil/pull/562#issuecomment-787508313) and just the textual info will do fine.

> Username: avinal  
> Created_at: 2021-03-01 05:07:41 UTC  
> Updated_at: 2021-03-01 05:07:42 UTC  
> Url: https://github.com/boostorg/gil/pull/567#discussion_r584448922  

Yeah this is just the textual part, you can see more details: https://docs.codecov.io/docs/pull-request-comments#layout  
Additionally, we may add files too.   
  
![image](https://user-images.githubusercontent.com/74113200/109454359-1319f880-7a7a-11eb-979d-891479c1f7ec.png)


---

## Comment 2

> Username: avinal  
> Created_at: 2021-03-01 11:03:58 UTC  
> Url: https://github.com/boostorg/gil/pull/567#issuecomment-787861734  

Thanks @mloskot 🥳

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-03-01 15:03:38 UTC  
> Url: https://github.com/boostorg/gil/pull/567#issuecomment-788017257  

Thanks for help @avinal

---
