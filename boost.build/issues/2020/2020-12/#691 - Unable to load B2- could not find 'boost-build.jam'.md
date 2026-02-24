# #691 - Unable to load B2: could not find 'boost-build.jam' [Closed]

> Username: sledgehammer999  
> Created at: 2020-12-26 22:20:48 UTC  
> Updated at: 2020-12-26 22:46:38 UTC  
> Closed at: 2020-12-26 22:46:38 UTC  
> Url: https://github.com/boostorg/build/issues/691  

This happens with boost 1.75 on Windows 7 and possibly with boost 1.74.  
  
I compile boost in place and then cd into my other project. Then I call b2 like this `..\boost_1_75_0\b2.exe <options> -sBOOST_ROOT=<path to boost folder`  
And immediately I get the above error.  
  
The same commandline worked fine up until boost 1.73.  
In fact, if I use the `b2` from 1.73 it compiles fine with boost 1.75 (although it complains about version mismatch in b2).  
  
Am I doing something wrong or is this a bug from the recent rewrite?

---

## Comment 1

> Username: sledgehammer999  
> Created at: 2020-12-26 22:46:38 UTC  
> Url: https://github.com/boostorg/build/issues/691#issuecomment-751403758  

Probably solved in 1.76. See comment: https://github.com/boostorg/build/issues/677#issuecomment-751402905
