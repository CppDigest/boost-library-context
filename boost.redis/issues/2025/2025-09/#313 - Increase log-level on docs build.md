# #313 - Increase log-level on docs build [Closed]

> Username: sdarwin  
> Created at: 2025-09-22 11:04:30 UTC  
> Updated at: 2025-09-24 10:53:43 UTC  
> Closed at: 2025-09-24 10:53:43 UTC  
> Url: https://github.com/boostorg/redis/issues/313  

The superproject is not building, it fails at redis. This is most likely a random error that will resolve itself.  Still, it is interesting to view more information.   Notice the debug flags from boostorg/url.   doc/build_antora.sh:      
  
```  
npx antora --clean --fetch "$PLAYBOOK" --stacktrace --log-level all  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2025-09-23 17:55:11 UTC  
> Updated at: 2025-09-23 17:55:26 UTC  
> Url: https://github.com/boostorg/redis/issues/313#issuecomment-3325008866  

`all` generates an extraordinarily big amount of debug information which I honestly dislike having in our builds, since it distracts from real useful information. Would `--stacktrace --log-level info` be enough?

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-09-23 18:02:22 UTC  
> Url: https://github.com/boostorg/redis/issues/313#issuecomment-3325029812  

> Would --stacktrace --log-level info be enough?  
  
Yes, that might be enough.    
  
  
```  
Run `npm audit` for details.  
+ npx antora --log-format=pretty redis-playbook.yml  
[23:12:57.801] [41mFATAL[49m (antora): [36mRequest failed with status code 403  
[0m[2mAdd the --stacktrace option to see the cause of the error.[22m[39m  
```
