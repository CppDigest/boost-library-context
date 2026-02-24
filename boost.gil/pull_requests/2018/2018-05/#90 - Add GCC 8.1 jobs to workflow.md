# #90 [circleci] Add GCC 8.1 jobs to workflow [Merged]

> Username: mloskot  
> Created at: 2018-05-20 09:52:58 UTC  
> Updated at: 2018-05-20 09:57:37 UTC  
> Merged at: 2018-05-20 09:57:19 UTC  
> Closed at: 2018-05-20 09:57:19 UTC  
> Url: https://github.com/boostorg/gil/pull/90  

Add GCC 8.1 to CircleCI workflow based on the recently added official Docker images https://hub.docker.com/r/library/gcc/tags/

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-05-20 09:57:12 UTC  
> Url: https://github.com/boostorg/gil/pull/90#issuecomment-390469868  

I've realised CircleCI is set to build only `develop` branch, so this PR won't be checked:  
  
```  
workflows:  
  version: 2  
  build-and-test:  
    jobs:  
      - bootstrap_checkout:  
          filters:  
            branches:  
              only: develop  
...  
```  
  
I'm going to merge this now and see follow up with any fixes required directly in `develop`.

---
