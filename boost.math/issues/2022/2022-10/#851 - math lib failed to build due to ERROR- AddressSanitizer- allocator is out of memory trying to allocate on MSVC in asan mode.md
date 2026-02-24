# #851 - math lib failed to build due to ERROR: AddressSanitizer: allocator is out of memory trying to allocate on MSVC in asan mode [Closed]

> Username: spacelg  
> Created at: 2022-10-21 10:19:24 UTC  
> Updated at: 2022-10-21 15:48:20 UTC  
> Closed at: 2022-10-21 15:48:20 UTC  
> Url: https://github.com/boostorg/math/issues/851  

Issue description:  
Found Boost\libs\math failed to build due to ERROR: AddressSanitizer: allocator is out of memory trying to allocate on MSVC in asan mode, this issue can be reproduced on 2b99278 commit. Could you please take a look?  
  
Reproduce steps:  
  
1.git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git f:\Boost  
2.open a VS 2019 x86 command prompt and browse to f:\Boost  
3.set CL= /fsanitize=address /GS- /wd5072 & set LINK= /InferASanLibs /incremental:no /debug  
4. .\bootstrap  
5. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
7. .\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\x86rel libs\math\test address-model=32  
  
[test.log](https://github.com/boostorg/math/files/9838134/test.log)  
  
ErrorMessage:  
=================================================================  
==2008==ERROR: AddressSanitizer: allocator is out of memory trying to allocate 0x10010 bytes  
==2008==FATAL: AddressSanitizer: internal allocator is out of memory trying to allocate 0x30 bytes

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-10-21 15:12:24 UTC  
> Url: https://github.com/boostorg/math/issues/851#issuecomment-1287101071  

I don't think this is a problem with the code, it's a problem with ASAN requesting more memory than exists on the runner, no?

---

## Comment 2

> Username: mborland  
> Created at: 2022-10-21 15:48:20 UTC  
> Url: https://github.com/boostorg/math/issues/851#issuecomment-1287145454  

Since you are having the exact same allocation error using [boost.log](https://github.com/boostorg/log/issues/198) this is a memory exhaustion issue. You'll need to reduce the number of threads, or increase your available memory.
