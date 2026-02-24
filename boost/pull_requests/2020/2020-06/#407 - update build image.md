# #407 update build image [Closed]

> Username: sdarwin  
> Created at: 2020-06-09 14:56:03 UTC  
> Updated at: 2021-03-24 15:58:40 UTC  
> Closed at: 2021-03-24 15:58:40 UTC  
> Url: https://github.com/boostorg/boost/pull/407  

Hi @mjcaisse,  
  
This includes build dependencies in the Docker image as we had discussed.  
The changes can be viewed here: https://github.com/sdarwin/boost_docker/tree/build_dependencies

---

## Comment 1

> Username: sdarwin  
> Created_at: 2020-06-24 20:48:58 UTC  
> Updated_at: 2020-06-24 20:49:11 UTC  
> Url: https://github.com/boostorg/boost/pull/407#issuecomment-649063550  

@mjcaisse   
  
> cppalliance isn't visible in docker hub.  
  
It should be visible, https://hub.docker.com/u/cppalliance  
  
And, the docker pull command does seem to work.  
```  
docker pull cppalliance/boost_superproject_build:build-deps-1  
```  
  
> moving it to boostorg  
  
Ok!

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-03-24 15:58:40 UTC  
> Url: https://github.com/boostorg/boost/pull/407#issuecomment-805947635  

This was already resolved. Closing PR.

---
