# #722 Avoid use of "local" in /bin/sh-based script [Closed]

> Username: tanzislam  
> Created at: 2021-05-02 10:42:11 UTC  
> Updated at: 2021-10-02 20:59:38 UTC  
> Closed at: 2021-05-03 18:47:52 UTC  
> Url: https://github.com/boostorg/build/pull/722  

Tried building Boost in Solaris 11.4 and got this:  
```  
+ ./bootstrap.sh --with-toolset=gcc  
Building B2 engine..  
./tools/build/src/engine/build.sh[114]: local: not found [No such file or directory]  
./tools/build/src/engine/build.sh[115]: local: not found [No such file or directory]  
./tools/build/src/engine/build.sh[116]: local: not found [No such file or directory]  
./tools/build/src/engine/build.sh[126]: local: not found [No such file or directory]  
./tools/build/src/engine/build.sh[40]: test: argument expected  
B2_TOOLSET is gcc, but the 'gcc' command cannot be executed.  
Make sure 'gcc' is in PATH, or use a different toolset.  
  
Failed to build B2 build engine  
```  
  
Looks like `/bin/sh` on Solaris doesn't have extra `bash`/`ksh` features, and [`local` is not in POSIX](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html).  
  
  Thank you for your contributions. Main development of B2 has moved to  
  https://github.com/bfgroup/b2

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:37 UTC  
> Url: https://github.com/boostorg/build/pull/722#issuecomment-932819676  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
