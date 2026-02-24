# #142 drone.jsonnet update [Merged]

> Username: sdarwin  
> Created at: 2022-08-19 17:04:47 UTC  
> Updated at: 2022-08-20 00:26:16 UTC  
> Merged at: 2022-08-19 20:15:26 UTC  
> Closed at: 2022-08-19 20:15:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/142  

Ideas for .drone.jsonnet:  
  
- A new set of macminis are running Monterey 12.4 with Xcode 12.5 through 13.4. If jobs are distributed across both Monterey and Catalina it can balance the load more.   
  
- Add 'uname' diagnostics at the beginning of each job to show the name of the runner and the architecture.  
  
- Append "/usr/local/bin" to PATH, which seems to be needed on OSX.  
  
@pdimov consider these changes for other repos also.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2022-08-20 00:26:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/142#issuecomment-1221186736  

It just occurred to me that you prefer using Mac OSX 10.15 to add coverage for older operating systems, not on GHA.  In that case, it's fine to put them back.  No problem.  
  
Another idea, maybe even better, is to add in the High Sierra machines, Xcode 6 through 9.  
  
```  
   macos_pipeline(  
        "MacOS 10.13 Xcode 9.4.1 ASAN",  
        { TOOLSET: 'clang', COMPILER: 'clang++', CXXSTD: '03,11,14,1z' } + asan,  
        xcode_version = "9.4.1", osx_version = "highsierra",  
    ),  
```

---
