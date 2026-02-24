# #369 update  [Closed]

> Username: ambreshbiradar9  
> Created at: 2020-06-12 14:35:27 UTC  
> Updated at: 2020-06-12 14:43:33 UTC  
> Closed at: 2020-06-12 14:43:33 UTC  
> Url: https://github.com/boostorg/math/pull/369  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-06-12 14:39:29 UTC  
> Url: https://github.com/boostorg/math/pull/369#issuecomment-643306883  

You are trying to merge commits you already merged. I would close out this PR and do the following:  
  
```  
math$ git checkout develop  
math$ git fetch upstream  
math$ git merge upstream/develop  
math$ git push  
math$ git checkout -b typos  
```  
  
Now make your changes.

---
