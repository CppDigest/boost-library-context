# #117 - Is Android supported? [Open]

> Username: Klayflash  
> Created at: 2021-12-20 11:52:03 UTC  
> Updated at: 2024-09-12 08:23:00 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/117  

Hello! Thanks for wonderful library. I use it on windows platform successfully.  
  
I tried to use on android platform but without success.  
Addresses are printed but ndk-stack can't symbolicate.  
  
example of boost stacktrace address:  
`0# 0xE8AA7B0F in /data/app/~~-pG4SOLsXq-askw4UHs24w==/com.example.Tests-3D6sv-1TK9A3AFG6r9TO8g==/lib/x86/libmain.so`  
  
addresses from [xunwind_cfi_get](https://github.com/hexhacking/xUnwind) function can be symbolicated successfully:  
`#00 pc 001b5548  /data/app/~~-pG4SOLsXq-askw4UHs24w==/com.example.Tests-3D6sv-1TK9A3AFG6r9TO8g==/lib/x86/libmain.so`
