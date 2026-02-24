# #475 Fix wide strings conversion on POSIX [Merged]

> Username: Osyotr  
> Created at: 2025-04-09 15:12:11 UTC  
> Updated at: 2025-04-14 16:11:09 UTC  
> Merged at: 2025-04-14 15:49:01 UTC  
> Closed at: 2025-04-14 15:49:01 UTC  
> Url: https://github.com/boostorg/process/pull/475  

This commit effectively reverts #179 which shouldn't have been merged in the first place. See https://github.com/boostorg/filesystem/pull/163#issuecomment-786794483 for explanation.  
  
For example, this code https://godbolt.org/z/rdvxh1EPz throws the following exception:  
```c++  
boost::process codecvt to char: error  
```  
  
Workaround is to manually call `boost::process::imbue` with the correct locale:  
```c++  
boost::process::imbue(std::locale(""));  
```

---
