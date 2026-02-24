# #9 if a file does not exist and BOOST_IOS::app specified, file_descriptor should create the file [Closed]

> Username: grynko  
> Created at: 2014-11-17 11:49:05 UTC  
> Updated at: 2016-12-09 03:06:57 UTC  
> Closed at: 2016-12-09 03:06:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/9  

file_descriptor should handle file open mode according to generally accepted convention and uniformly on all platforms:  
  
if both BOOST_IOS::out and BOOST_IOS::app specified, the class must create the file if it doesn't exist (just as it's done in Windows implementation).

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-12-03 23:55:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/9#issuecomment-264673997  

PR are never made against the 'master' branch.

---

## Comment 2

> Username: eldiener  
> Created_at: 2016-12-09 03:06:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/9#issuecomment-265921958  

This has been fixed in the 'develop' branch.

---
