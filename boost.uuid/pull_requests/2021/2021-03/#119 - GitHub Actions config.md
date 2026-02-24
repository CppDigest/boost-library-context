# #119 GitHub Actions config [Closed]

> Username: sdarwin  
> Created at: 2021-03-05 16:28:12 UTC  
> Updated at: 2022-01-25 21:51:29 UTC  
> Closed at: 2022-01-25 21:51:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/119  

GitHub Actions CI script, generated from the .travis.yml file. Please refer to https://github.com/CPPAlliance/githubactions for more information and instructions.

---

## Comment 1

> Username: jeking3  
> Created_at: 2021-06-15 16:39:50 UTC  
> Url: https://github.com/boostorg/uuid/pull/119#issuecomment-861656854  

How come we skipped ci on this?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-06-15 17:11:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/119#issuecomment-861678286  

I believe that Github Actions won't run the first time, only from a pull request.   It needs to be merged into the repository.  So, having "skip ci" on a new pull request didn't affect Github Actions.     And, Travis should not be affected by a single file .github/workflows/ci.yml.

---

## Comment 3

> Username: jeking3  
> Created_at: 2021-06-15 18:08:01 UTC  
> Updated_at: 2021-06-15 18:10:42 UTC  
> Url: https://github.com/boostorg/uuid/pull/119#issuecomment-861722319  

GitHub Actions will run on pull requests... but there may be limits on what happens from a fork.

---
