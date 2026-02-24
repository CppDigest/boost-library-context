# #284 - directory_iterator is broken for smb1 locations on windows [Closed]

> Username: cdecker08  
> Created at: 2023-04-13 16:15:40 UTC  
> Updated at: 2023-04-14 12:25:46 UTC  
> Closed at: 2023-04-14 12:24:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/284  

`directory` is a smb1 location, either a mapped network drive or UNC path  
  
```  
bool isDir = fs::is_directory(directory); <- Works  
fs::directory_iterator iter(directory); <- throws an exception  
```  
  
The exception seems to originate at directory.cpp:842 with an error code of 124 (ERROR_INVALID_LEVEL)

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-13 16:52:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/284#issuecomment-1507286456  

Could you test if https://github.com/boostorg/filesystem/commit/fcc1bf3eca15323b90f2df042464668b0d131698 fixes the problem for you?

---

## Comment 2

> Username: cdecker08  
> Created at: 2023-04-13 17:52:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/284#issuecomment-1507385077  

This works for me locally, let me get a test build including this patch out to some users in order to do the final verification.

---

## Comment 3

> Username: cdecker08  
> Created at: 2023-04-14 12:06:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/284#issuecomment-1508404189  

This seems to work for our users.  There appear to be some users with miscellaneous issues not related to smbv1, however I hesitate to file any bugs unless we can reproduce or at least establish a pattern.

---

## Comment 4

> Username: Lastique  
> Created at: 2023-04-14 12:25:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/284#issuecomment-1508430911  

Ok, thanks for the report and testing.
