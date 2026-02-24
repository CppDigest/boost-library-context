# #186 Fix build with Python 3.7 [Merged]

> Username: berolinux  
> Created at: 2018-02-09 17:23:34 UTC  
> Updated at: 2018-02-09 20:09:39 UTC  
> Merged at: 2018-02-09 20:09:28 UTC  
> Closed at: 2018-02-09 20:09:28 UTC  
> Url: https://github.com/boostorg/python/pull/186  

Python 3.7 changes the return type of _PyUnicode_AsString()  
from void* to const char* -- causing the build of boost-python  
to fail.  
  
Signed-off-by: Bernhard Rosenkränzer <bero@lindev.ch>

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-02-09 20:09:39 UTC  
> Url: https://github.com/boostorg/python/pull/186#issuecomment-364550289  

Thanks !

---
