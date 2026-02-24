# #245 - C2065: 'DEBUG_STATE': undeclared identifier [Closed]

> Username: hia3  
> Created at: 2017-09-29 15:25:40 UTC  
> Updated at: 2017-12-06 01:26:58 UTC  
> Closed at: 2017-12-06 01:26:58 UTC  
> Url: https://github.com/boostorg/build/issues/245  

Building debug version of b2 with:  
  
    bootstrap.bat --debug  
  
fails:  
  
    debugger.c(1103): error C2065: 'DEBUG_STATE': undeclared identifier
