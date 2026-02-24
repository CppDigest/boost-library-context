# #626 - bootstrap.bat does not respect overridden gcc toolset [Open]

> Username: amyspark  
> Created at: 2022-02-22 00:09:59 UTC  
> Updated at: 2024-10-01 19:47:50 UTC  
> Url: https://github.com/boostorg/boost/issues/626  

Hi,  
  
Due to msys2/MINGW-packages#10800 and https://github.com/bfgroup/b2/issues/138, I need to add a `user-config.jam` to bootstrap b2 correctly:  
  
```  
using gcc : : D:/msys64/clang64/bin/clang++.exe ;  
```  
  
However, the bootstrap batch file for boost completely ignores the overridden `gcc` toolset:  
  
https://github.com/boostorg/boost/blob/2b06d9f20560a4f7292833fcb71d8c6a3652ca76/bootstrap.bat#L49-L59  
  
whereas the Bash version does not:  
  
https://github.com/boostorg/boost/blob/2b06d9f20560a4f7292833fcb71d8c6a3652ca76/bootstrap.sh#L337-L354  
  
so I need to yank project-config.jam prior to launching b2.

---

## Comment 1

> Username: ptmartellock  
> Created at: 2024-10-01 19:47:48 UTC  
> Url: https://github.com/boostorg/boost/issues/626#issuecomment-2386902319  

I had this problem too.  I was cross compiling from linux on x86 targeting aarch64.  I had to create softlinks on the path to point to the compilers for g++ and gcc:    
  
The softlinks are on the path before the native g++ and gcc compilers.  
  
  lrwxrwxrwx. 1 paul paul   21 Oct  1 13:59 g++ -> aarch64-linux-gnu-g++  
  lrwxrwxrwx. 1 paul paul   21 Oct  1 13:59 gcc -> aarch64-linux-gnu-gcc  
  
My user-config contains:   
  using gcc : 9.2 : aarch64-linux-gnu-gcc   
  
My take-away is the last part of the using did not select the gcc executable, It seems to always point to gcc instead.
