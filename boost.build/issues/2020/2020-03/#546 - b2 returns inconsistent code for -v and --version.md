# #546 - b2 returns inconsistent code for -v and --version [Open]

> Username: mloskot  
> Created at: 2020-03-09 23:49:41 UTC  
> Updated at: 2021-05-29 18:22:28 UTC  
> Url: https://github.com/boostorg/build/issues/546  

```  
$ ./b2 --version  
Boost.Build 4.2-git  
  
$ echo $?  
1  
```  
  
vs  
  
```  
$ ./b2 -v  
B2 Version 4.2. OS=LINUX.  
  Copyright 1993-2002 Christopher Seiwald and Perforce Software, Inc.  
  Copyright 2001 David Turner.  
  Copyright 2001-2004 David Abrahams.  
  Copyright 2002-2019 Rene Rivera.  
  Copyright 2003-2015 Vladimir Prus.  
  
  DEFAULTS: jobs = 40  
  
$ echo $?  
0  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:57 UTC  
> Url: https://github.com/boostorg/build/issues/546#issuecomment-850877298  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
