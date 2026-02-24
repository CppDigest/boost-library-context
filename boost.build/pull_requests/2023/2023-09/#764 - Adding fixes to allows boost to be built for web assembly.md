# #764 Adding fixes to allows boost to be built for web assembly [Closed]

> Username: bruno-j-nicoletti  
> Created at: 2023-09-01 10:38:40 UTC  
> Updated at: 2023-09-01 10:38:54 UTC  
> Closed at: 2023-09-01 10:38:53 UTC  
> Url: https://github.com/boostorg/build/pull/764  

PR to allow boost to be built for web assembly.  
  
See https://github.com/boostorg/regex/issues/59#issuecomment-828707747  
  
Without this you get the error message...  
  
```  
Error: ambiguity found when searching for best transformation  
Trying to produce type 'SEARCHED_LIB' from:  
Generators that succeeded:  
 -  searched-lib-generator  
 -  emscripten.searched-lib-generator  
First generator produced:  
 -  { %.no-action-psapi.SEARCHED_LIB }  
Second generator produced:  
 -  { %.no-action-psapi.SEARCHED_LIB }  
 ```

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2023-09-01 10:38:51 UTC  
> Url: https://github.com/boostorg/build/pull/764#issuecomment-1702542269  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
