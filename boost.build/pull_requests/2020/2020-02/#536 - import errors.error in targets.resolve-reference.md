# #536 import errors.error in targets.resolve-reference. [Merged]

> Username: jehelset  
> Created at: 2020-02-05 08:56:49 UTC  
> Updated at: 2021-10-02 21:13:31 UTC  
> Merged at: 2020-02-09 14:38:51 UTC  
> Closed at: 2020-02-09 14:38:51 UTC  
> Url: https://github.com/boostorg/build/pull/536  

got an unknown-error instead of error-message without the import.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-02-09 03:16:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/536#pullrequestreview-355581020  

📁 src/build/targets.jam

```diff
 888 |     if ! $(split) || ! $(id)
 889 |     {
 890 |+         IMPORT errors : error : : error ;
```

> Username: grafikrobot  
> Created_at: 2020-02-09 03:16:03 UTC  
> Updated_at: 2020-02-09 13:00:18 UTC  
> Url: https://github.com/boostorg/build/pull/536#discussion_r376751139  

Should be something like this instead:  
```  
import errors ;  
errors.error "..." ;  
```


---
