# #611 Dedicated build for coveralls [Merged]

> Username: jszuppe  
> Created at: 2016-05-08 15:44:33 UTC  
> Updated at: 2016-05-31 22:23:14 UTC  
> Merged at: 2016-05-11 05:06:55 UTC  
> Closed at: 2016-05-11 05:06:55 UTC  
> Url: https://github.com/boostorg/compute/pull/611  

This PR introduces minor changes to Travis-CI build script.  
1. Now there is one dedicated build for Coveralls (Trusty, g++ 4.8, AMD APP SDK v2.9.1, OpenCL 1.2). It should make POCL builds slightly faster. Unfortunately, it's not possible to use g++ 4.8 on Precise (Travis CI container-based infrastructure) and get coverage from it (only g++ builds give coverage info).  
2. Now we do not push coverage info of tests to the Coveralls.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-05-08 16:49:11 UTC  
> Url: https://github.com/boostorg/compute/pull/611#issuecomment-217732445  

[![Coverage Status](https://coveralls.io/builds/6092815/badge)](https://coveralls.io/builds/6092815)  
  
Coverage decreased (-9.4%) to 79.435% when pulling **81f2815b664fcd43523c555e714de08a609dd5bc on haahh:pr_coveralls_build** into **dc7bfa0252519592c44c764a4578dbe14b76b37f on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-05-09 11:56:09 UTC  
> Url: https://github.com/boostorg/compute/pull/611#issuecomment-217844805  

[![Coverage Status](https://coveralls.io/builds/6100583/badge)](https://coveralls.io/builds/6100583)  
  
Coverage decreased (-8.4%) to 80.471% when pulling **476b15933d1cfec7249b8dc8be0ce28ee448d2d0 on haahh:pr_coveralls_build** into **dc7bfa0252519592c44c764a4578dbe14b76b37f on boostorg:develop**.

---
