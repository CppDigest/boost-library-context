# #285 remove TRAVIS_OS_NAME from drone script [ci skip] [Merged]

> Username: sdarwin  
> Created at: 2021-01-07 19:11:55 UTC  
> Updated at: 2021-01-07 19:37:40 UTC  
> Merged at: 2021-01-07 19:37:40 UTC  
> Closed at: 2021-01-07 19:37:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/285  

Small change to drone file. The advantage of removing TRAVIS_OS_NAME from these scripts, and instead setting it in [functions.star](https://github.com/boostorg/boost-ci/blob/master/ci/drone/functions.star) is that it will always be set correctly and not depend on the end-user to configure variables like DRONE_JOB_OS_NAME.

---
