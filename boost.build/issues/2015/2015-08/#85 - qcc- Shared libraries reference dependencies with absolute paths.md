# #85 - qcc: Shared libraries reference dependencies with absolute paths [Closed]

> Username: aelkhour  
> Created at: 2015-08-10 09:45:09 UTC  
> Updated at: 2017-02-12 21:46:57 UTC  
> Closed at: 2017-02-12 21:46:57 UTC  
> Url: https://github.com/boostorg/build/issues/85  

I tried building boost 1.57 for qnxnto using   
`./b2 toolset=qcc target-os=qnxnto variant=release link=shared install`  
and I encountered the same issue as the one described here https://github.com/blackberry/Boost/issues/11  
  
The proposed solution of removing HAVE_SONAME in qcc.jam worked for me, and I was wondering:  
- whether this issue has been solved since 1.57,  
- whether removing SO_NAME is a clean solution.

---

## Comment 1

> Username: vprus  
> Created at: 2015-11-19 18:11:41 UTC  
> Url: https://github.com/boostorg/build/issues/85#issuecomment-158141209  

Antonio,  
  
there were not changes. Removing HAVE_SONAME is a suitable solution. Since qnx apparently has soname option in the compiler, there is no reason to be overly generic in qcc.jam.  
  
Possible, it is possible to entirely remove definition of link.dll function and action from qcc.jam, so that it inherits one from gcc jam, but I don't have any way to test such a change, and don't recall why qcc.jam overrides these.
