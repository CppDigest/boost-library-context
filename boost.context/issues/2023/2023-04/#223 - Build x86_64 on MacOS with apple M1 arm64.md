# #223 - Build x86_64 on MacOS with apple M1 arm64. [Closed]

> Username: TemplateVoid  
> Created at: 2023-04-06 17:28:46 UTC  
> Updated at: 2023-08-18 05:06:06 UTC  
> Closed at: 2023-08-18 05:06:06 UTC  
> Url: https://github.com/boostorg/context/issues/223  

Hi! I am trying to build x86_64-binaries on apple m1 host-machine with next command:   
`cmake ../test -DCMAKE_INSTALL_PREFIX=../Install -DCMAKE_OSX_ARCHITECTURES=x86_64`  
  
It gives me the following results:  
  
> -- Boost.Context: architecture arm64, binary format mach-o, ABI aapcs, assembler gas, suffix .S, implementation fcontext  
  
Is it possible to build x86_64 binaries this way? Or i need x86_64 host machine...

---

## Comment 1

> Username: kassane  
> Created at: 2023-06-18 19:42:52 UTC  
> Url: https://github.com/boostorg/context/issues/223#issuecomment-1596247372  

You can try with this fork to cross-compile:  
https://github.com/kassane/context (w/ zig build).  
Unfortunately only arm64-windows got problems with the masm extension as it supports mingw.
