# #616 - Use --out-implib <name> instead of linking with dll directly when compiling Boost with clang in mingw mode [Closed]

> Username: longnguyen2004  
> Created at: 2020-06-06 04:42:41 UTC  
> Updated at: 2020-07-14 06:55:02 UTC  
> Closed at: 2020-07-14 06:55:02 UTC  
> Url: https://github.com/boostorg/build/issues/616  

ld.lld on Windows doesn't support linking directly with dll files yet, the proper way to link with them is to create an import library with ```--out-implib``` and use that instead
