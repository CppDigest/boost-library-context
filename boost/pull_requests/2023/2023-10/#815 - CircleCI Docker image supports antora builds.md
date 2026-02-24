# #815 CircleCI Docker image supports antora builds [Merged]

> Username: alandefreitas  
> Created at: 2023-10-12 21:35:02 UTC  
> Updated at: 2023-10-16 12:34:30 UTC  
> Merged at: 2023-10-13 22:43:50 UTC  
> Closed at: 2023-10-13 22:43:50 UTC  
> Url: https://github.com/boostorg/boost/pull/815  

Updates CircleCI Docker image to `cppalliance/boost_superproject_build:20.04-v3`, which includes nodejs.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2023-10-12 21:36:53 UTC  
> Url: https://github.com/boostorg/boost/pull/815#issuecomment-1760396602  

@sdarwin

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-10-13 19:30:53 UTC  
> Url: https://github.com/boostorg/boost/pull/815#issuecomment-1762082784  

@pdimov

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-10-16 11:08:55 UTC  
> Url: https://github.com/boostorg/boost/pull/815#issuecomment-1764238954  

After merging this PR jobs fail with  
  
```  
Starting container cppalliance/boost_superproject_build:20.04-v3  
Warning: No authentication provided, using CircleCI credentials for pulls from Docker Hub.  
  image cache not found on this host, downloading cppalliance/boost_superproject_build:20.04-v3  
  
Error response from daemon: manifest for cppalliance/boost_superproject_build:20.04-v3 not found: manifest unknown: manifest unknown  
```  
  
https://app.circleci.com/pipelines/github/boostorg/boost/10544/workflows/9c3afb33-9127-49be-85f1-858be261b82e/jobs/25277

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-10-16 12:34:29 UTC  
> Url: https://github.com/boostorg/boost/pull/815#issuecomment-1764379013  

Should be fixed. Check the next builds.

---
