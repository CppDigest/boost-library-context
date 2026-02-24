# #30 Provide extension point so library user can provide default temp folder [Merged]

> Username: stheophil  
> Created at: 2016-09-19 15:07:24 UTC  
> Updated at: 2016-11-13 18:18:10 UTC  
> Merged at: 2016-11-13 18:15:17 UTC  
> Closed at: 2016-11-13 18:15:17 UTC  
> Url: https://github.com/boostorg/interprocess/pull/30  

(That's a slightly changed version of my previous PR.)  
  
The /tmp folder is not accessible for sandboxed Applications on the Mac. The docs are clear that you have to get the correct folder via the Mac APIs.  
  
Several sandboxed applications can define an application group which has shared access rights to some filesystem locations. In order to get the shared temp folder for the application group, the user must know the group identifier string and then call   
  
```  
[[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:(NSString *)groupIdentifier]  
```  
  
The returned path is user-specific and thus a hard-coded `BOOST_INTERPROCESS_SHARED_DIR_PATH` doesn't do.

---

## Comment 1

> Username: stheophil  
> Created_at: 2016-09-22 10:33:04 UTC  
> Url: https://github.com/boostorg/interprocess/pull/30#issuecomment-248865621  

I've simplified my change. There is now a define `BOOST_INTERPROCESS_SHARED_DIR_FUNC` that can be used instead of `BOOST_INTERPROCESS_SHARED_DIR_PATH` when the shared folder has to be determined at runtime. Both must define the full shared folder that the temp files will be placed in. No subfolder will be created. I've also added documentation.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2016-11-13 18:18:10 UTC  
> Url: https://github.com/boostorg/interprocess/pull/30#issuecomment-260202308  

Thanks for the patch!

---
