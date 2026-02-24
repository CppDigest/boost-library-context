# #22 update drone [Closed]

> Username: sdarwin  
> Created at: 2021-02-04 15:14:03 UTC  
> Updated at: 2021-04-21 18:08:58 UTC  
> Closed at: 2021-04-21 18:08:58 UTC  
> Url: https://github.com/boostorg/conversion/pull/22  

The script generating drone config files from .travis.yml expects to find an "install" section and a "script" section. This repo has been updated to move "before_install" to "install", so the resulting config is more consolidated and maintainable.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-04-21 18:08:58 UTC  
> Url: https://github.com/boostorg/conversion/pull/22#issuecomment-824256798  

Many thanks for your work!  
  
I'll stick to the GithubAction workflow from https://github.com/boostorg/boost-ci/blob/master/ci.yml with minor changes for coverage and inspect tool  
  
Another CI config is too much for me to handle

---
