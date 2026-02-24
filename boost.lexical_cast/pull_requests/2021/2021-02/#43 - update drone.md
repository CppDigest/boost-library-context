# #43 update drone [Closed]

> Username: sdarwin  
> Created at: 2021-02-04 15:14:51 UTC  
> Updated at: 2021-05-04 07:12:55 UTC  
> Closed at: 2021-05-04 07:12:55 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/43  

The script generating drone config files from .travis.yml expects to find an "install" section and a "script" section. This repo has been updated to move "before_install" to "install", so the resulting config is more consolidated and maintainable.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-05-04 07:12:55 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/43#issuecomment-831730797  

Many thanks for your work!  
  
I'll stick to the GithubAction workflow from https://github.com/boostorg/boost-ci/blob/master/ci.yml with minor changes for coverage and inspect tool  
  
Another CI config is too much for me to handle

---
