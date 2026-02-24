# #235 - fix "rm is not recognized" error when compiling with Clang on Windows [Merged]

> Username: SSE4  
> Created at: 2017-09-03 02:46:03 UTC  
> Updated at: 2021-10-02 22:08:36 UTC  
> Merged at: 2017-09-03 03:59:09 UTC  
> Closed at: 2017-09-03 03:59:09 UTC  
> Url: https://github.com/boostorg/build/pull/235  



---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2017-09-03 02:52:55 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/235#pullrequestreview-60286952  

Please adjust to use the common definition for RM.

📁 src/tools/clang-linux.jam

```diff
 156 |+     RM = rm -f ;
 157 |+ }
 158 |+ 
```

> Username: grafikrobot  
> Created_at: 2017-09-03 02:52:13 UTC  
> Updated_at: 2017-09-03 03:07:48 UTC  
> Url: https://github.com/boostorg/build/pull/235#discussion_r136707177  

There's already an existing definition of RM that you can use. Just do:  
```  
import common ;  
RM = [ common.rm-command ] ;  
```

> Username: SSE4  
> Created_at: 2017-09-03 03:50:26 UTC  
> Url: https://github.com/boostorg/build/pull/235#discussion_r136707803  

done, thanks for pointing


---
