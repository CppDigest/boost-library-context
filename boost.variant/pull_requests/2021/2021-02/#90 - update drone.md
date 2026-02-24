# #90 update drone [Closed]

> Username: sdarwin  
> Created at: 2021-02-04 15:17:24 UTC  
> Updated at: 2021-09-20 18:22:09 UTC  
> Closed at: 2021-09-20 18:22:09 UTC  
> Url: https://github.com/boostorg/variant/pull/90  

The script generating drone config files from .travis.yml expects to find an "install" section and a "script" section. This repo has been updated to move "before_install" to "install", so the resulting config is more consolidated and maintainable.

---

## Comment 1

> Username: coveralls  
> Created_at: 2021-02-08 02:39:15 UTC  
> Updated_at: 2021-02-19 20:31:34 UTC  
> Url: https://github.com/boostorg/variant/pull/90#issuecomment-774828259  

[![Coverage Status](https://coveralls.io/builds/37284956/badge)](https://coveralls.io/builds/37284956)  
  
Coverage remained the same at 93.421% when pulling **fa96cbfaafb9707c016d428562826fe5fb6085de on sdarwin:drone2** into **89424892447ed252ebd4232ce6ebb664e58d71ba on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2021-09-20 18:22:09 UTC  
> Url: https://github.com/boostorg/variant/pull/90#issuecomment-923169124  

Thanks for the PR, but I'm planning to stick to Github Actions and Appveyor

---
