# #198 - log lib failed to build due to ERROR: AddressSanitizer: allocator is out of memory trying to allocate on MSVC in asan mode [Closed]

> Username: spacelg  
> Created at: 2022-10-21 10:14:59 UTC  
> Updated at: 2022-10-21 10:19:42 UTC  
> Closed at: 2022-10-21 10:19:42 UTC  
> Url: https://github.com/boostorg/log/issues/198  

Issue description:  
Found Boost\libs\log failed to build due to ERROR: AddressSanitizer: allocator is out of memory trying to allocate on MSVC in asan mode, this issue can be reproduced on 34304f2 commit. Could you please take a look?  
  
Reproduce steps:  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git f:\Boost  
2. open a VS 2019 x86 command prompt and browse to f:\Boost  
3. set _CL_= /fsanitize=address /GS- /wd5072 & set _LINK_= /InferASanLibs /incremental:no /debug  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
7. .\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\x86rel libs\log\test address-model=32  
  
[test.log](https://github.com/boostorg/log/files/9838097/test.log)  
  
ErrorMessage:  
...  
=================================================================  
==8680==ERROR: AddressSanitizer: allocator is out of memory trying to allocate 0x10010 bytes  
==8680==FATAL: AddressSanitizer: internal allocator is out of memory trying to allocate 0x30 bytes

---

## Comment 1

> Username: Lastique  
> Created at: 2022-10-21 10:19:42 UTC  
> Url: https://github.com/boostorg/log/issues/198#issuecomment-1286754836  

Sorry, I won't be debugging ASan, please ask Microsoft for support. Also, see if you have enough free memory to build. You may try reducing the number of build jobs to reduce memory consumption.
