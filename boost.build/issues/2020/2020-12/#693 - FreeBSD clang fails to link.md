# #693 - FreeBSD clang fails to link [Closed]

> Username: arvidn  
> Created at: 2020-12-28 11:28:48 UTC  
> Updated at: 2020-12-28 11:46:21 UTC  
> Closed at: 2020-12-28 11:33:46 UTC  
> Url: https://github.com/boostorg/build/issues/693  

I suspect [this](https://github.com/boostorg/build/commit/f62f474ed336e1279505371946ae90e83b5252d5) patch broke FreeBSD build for me. I was using develop on CI, which started failing recently with this error message during the link step:  
```  
/bin/sh: cannot open =@:>=: No such file or directory  
```  
  
Here's the full log from cirrus: https://cirrus-ci.com/task/4560696987877376?command=tests#L197  
  
Reverting to 1.74.0 resolve the issue for me.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-28 11:33:46 UTC  
> Url: https://github.com/boostorg/build/issues/693#issuecomment-751682644  

The problem is that the b2 engine you are running needs to be up to date with the version of the build system code. As indicated by this message:  
```  
warning: mismatched versions of B2 engine and core  
warning: B2 engine (bjam) is 4.0.1  
warning: B2 core (at /tmp/cirrus-ci-build/build-develop/src) is 4.4-git  
```  
https://cirrus-ci.com/task/4560696987877376?command=tests#L4

---

## Comment 2

> Username: arvidn  
> Created at: 2020-12-28 11:46:21 UTC  
> Url: https://github.com/boostorg/build/issues/693#issuecomment-751685618  

I suppose I failed to run `bjam` from the package I downloaded from github, and instead ran the system-installed version..
