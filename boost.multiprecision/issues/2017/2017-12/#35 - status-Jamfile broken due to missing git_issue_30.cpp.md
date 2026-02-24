# #35 - status/Jamfile broken due to missing git_issue_30.cpp [Closed]

> Username: pdimov  
> Created at: 2017-12-26 16:42:31 UTC  
> Updated at: 2017-12-26 17:10:00 UTC  
> Closed at: 2017-12-26 17:10:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/35  

```  
error: Unable to find file or target named  
error:     'git_issue_30.cpp'  
error: referred to from project at  
error:     '../libs/multiprecision/test'  
```  
  
https://travis-ci.org/boostorg/boost/jobs/321519385#L8799

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-12-26 17:10:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/35#issuecomment-353990757  

Yep, just seen that and pushed the file.
