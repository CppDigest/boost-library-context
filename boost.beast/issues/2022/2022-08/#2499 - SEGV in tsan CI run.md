# #2499 - SEGV in tsan CI run [Closed]

> Username: madmongo1  
> Created at: 2022-08-16 08:02:39 UTC  
> Updated at: 2022-08-16 10:22:48 UTC  
> Closed at: 2022-08-16 10:22:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2499  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
330  
  
### Steps necessary to reproduce the problem  
  
```  
$ ./b2 toolset=gcc thread-sanitizer=norecover link=static variant=debug libs/beast/test -q -d+2 -j1  
```  
  
### All relevant compiler information  
  
GCC12  
  
There is bug/lack of feature in TSAN such that it does not correctly handle a fibre being resumed on a different thread.  
This is resulting in a SEGV during a tsan run.
