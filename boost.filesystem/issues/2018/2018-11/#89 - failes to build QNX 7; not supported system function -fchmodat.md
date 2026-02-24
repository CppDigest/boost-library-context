# #89 - failes to build QNX 7; not supported system function "fchmodat" [Closed]

> Username: Johnnyxy  
> Created at: 2018-11-12 14:02:36 UTC  
> Updated at: 2018-11-12 14:54:22 UTC  
> Closed at: 2018-11-12 14:53:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/89  

Hi there,  
I tried to build boost for QNX 7 (compiler qcc 5.4.0; under the hood GCC 5.4.0). the compilation stops in operations.cpp at line https://github.com/boostorg/filesystem/blob/6f89f73d7e0dc45f8d26a8c627f59730d10ebb03/src/operations.cpp#L1497  
  
with the following error  
  
`error: '::fchmodat' has not been declared`  
  
QNX does not support the *"fchmodat"* function. For other systems like Solaris there is a preprocessor check if the function is supported. This preprocessor check misses QNX.  
By adding the following line to the preprocessor check at line https://github.com/boostorg/filesystem/blob/6f89f73d7e0dc45f8d26a8c627f59730d10ebb03/src/operations.cpp#L1492  
  
`&& !(defined(__QNX__) && (_NTO_VERSION <= 700)) \`  
  
I was able to compile for QNX 7 successfully.   
The version check is deliberately included as future versions of QNX could add the *"fchmodat"* function (QNX adapts functionality slowly).  
  
Best regards

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-12 14:54:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/89#issuecomment-437910261  

Workaround committed, thanks for the report.
