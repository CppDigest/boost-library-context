# #307 - [Windows] Warnings from c++ compiler slow down the compilation [Closed]

> Username: hia3  
> Created at: 2018-05-16 05:42:51 UTC  
> Updated at: 2018-10-18 02:37:23 UTC  
> Closed at: 2018-10-18 02:37:23 UTC  
> Url: https://github.com/boostorg/build/issues/307  

Let's say I want to compile some code:  
  
    clang++.exe my_source.cpp  
  
It produces 2MB of warnings on stdout and takes 5 seconds to finish the compilation.  
  
If that same command is executed by `b2.exe`, it will take 5 minutes (depending on amount of stdout).  
  
To reproduce you'll need `2mb_text_file` of size 2MB, empty file named `the.in`  and following `Jamroot`:  
  
    make the.out : the.in : @do-something ;  
    actions do-something  
    {  
        type 2mb_text_file  
    }  
  
b2 invocation will take a lot of time:  
  
    b2.exe -sBOOST_ROOT=c:\boost  
  
Changing [this](https://github.com/boostorg/build/blob/develop/src/engine/execnt.c#L418) value from 500 to 1 helps quite a lot. My guess is that child process on Windows is unable to progress if parent is not doing things.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-11 17:43:39 UTC  
> Url: https://github.com/boostorg/build/issues/307#issuecomment-429051534  

Nice finding! I thought that compilers are slow at reporting warnings/errors, but it turns out that the problem is in B2.  
  
It looks like there is an OS pipe buffer with size around 4KB (`PeekNamedPipe` returns either 4176 or 4128 for me and never more). I have tried to increase this buffer with `SetNamedPipeHandleState`, but it has no effect.  
  
The good news is that any attempt to read the pipe (msdn says that `PeekNamedPipe` is a just a non clearing version of `ReadFile`) pumps it. So, if we just make another loop iteration it will return the data. It is simple as commenting out the next line   
https://github.com/boostorg/build/blob/bc7659b9afe79f6ea8e70e8ea4a6974286d67d0d/src/engine/execnt.c#L803  
  
The good fix is to use async IO, but as for now I have opened a PR #353.
