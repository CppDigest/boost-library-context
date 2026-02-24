# #622 - build boost [Open]

> Username: alicera  
> Created at: 2022-02-15 03:48:44 UTC  
> Updated at: 2022-02-15 03:48:44 UTC  
> Url: https://github.com/boostorg/boost/issues/622  

https://www.boost.org/doc/libs/1_78_0/tools/build/doc/html/index.html#bbv2.installation  
I try to build release and debug with a batch file.  
Should I run twice bootstrap.bat and remove the build folder for release?  
Avoid the release lib will be built on debug.   
``` batch file  
bootstrap.bat  
b2 install --prefix=release variant=release --build-dir=build  
del build  
b2 install --prefix=debug variant=debug --build-dir=build  
```
