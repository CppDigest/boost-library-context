# #717 Handle spaces in CXX path in config_toolset.bat [Closed]

> Username: NeroBurner  
> Created at: 2021-03-26 21:21:25 UTC  
> Updated at: 2021-10-02 20:59:14 UTC  
> Closed at: 2021-03-26 21:38:20 UTC  
> Url: https://github.com/boostorg/build/pull/717  

Surround the `CXX` path with double quotes to support spaces in the path.  
Otherwise if the `CXX` variable is set to a path containing spaces like  
  
```  
C:/Program Files (x86)/Microsoft Visual Studio/2017/BuildTools/VC/Tools/MSVC/14.16.27023/bin/Hostx64/x64/cl.exe  
```  
  
the following error message is printed:  
  
```  
'C:/Program' is not recognized as an internal or external command,  
operable program or batch file.  
```

---

## Comment 1

> Username: mloskot  
> Created_at: 2021-03-26 21:24:20 UTC  
> Url: https://github.com/boostorg/build/pull/717#issuecomment-808516262  

https://lists.boost.org/Archives/boost/2021/03/251031.php says:  
  
> And hence PRs for B2 will need to be posted to the new repo.   
  
https://github.com/bfgroup/b2

---

## Comment 2

> Username: NeroBurner  
> Created_at: 2021-03-26 21:36:54 UTC  
> Url: https://github.com/boostorg/build/pull/717#issuecomment-808521793  

opened https://github.com/bfgroup/b2/pull/13  
  
should I close this MR?

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-03-26 21:37:44 UTC  
> Url: https://github.com/boostorg/build/pull/717#issuecomment-808522153  

Yes, I think so.  
/cc @grafikrobot

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:13 UTC  
> Url: https://github.com/boostorg/build/pull/717#issuecomment-932819605  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
