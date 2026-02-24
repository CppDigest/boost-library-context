# #14 - Can't compile libs/context/src/asm/make_arm64_aapcs_macho_gas.S [Closed]

> Username: gdochev  
> Created at: 2015-05-01 18:21:03 UTC  
> Updated at: 2021-01-15 08:09:35 UTC  
> Closed at: 2016-10-11 08:11:25 UTC  
> Url: https://github.com/boostorg/context/issues/14  

Hi, I am trying to cross-compile the context library in 1.58 targeting iOS on an OSX host. The library builds fine for arm, but not for the newly introduced arm64. When running with:  
  
```  
./bjam                                                                                 
    -j8                                                                                   
    --build-dir=iphone-build                                                  
    --stagedir=iphone-build/stage                                        
    --prefix=$PREFIXDIR                                                    
    --toolset=darwin-${IPHONE_SDKVERSION}~iphone   
    toolset=darwin                                                               
    architecture=arm                                                           
    target-os=iphone                                                            
    address-model=64                                                         
    binary-format=mach-o                                                    
    abi=aapcs                                                                       
    macosx-version=iphone-${IPHONE_SDKVERSION}    
    define=_LITTLE_ENDIAN                                              
    link=static                                                                       
    threading=multi                                                              
    stage  
```  
  
I get a lot of those:  
  
libs/context/src/asm/make_arm64_aapcs_macho_gas.S:54:21: error: unexpected token in argument list  
    ; shift address in x0 (allocated stack) to lower 16 byte boundary  
                    ^  
libs/context/src/asm/make_arm64_aapcs_macho_gas.S:55:17: error: unexpected token in operand  
    and x0, x0, ~0xF  
                ^  
libs/context/src/asm/make_arm64_aapcs_macho_gas.S:57:21: error: unexpected token in argument list  
    ; reserve space for context-data on context-stack  
                    ^  
libs/context/src/asm/make_arm64_aapcs_macho_gas.S:58:9: error: invalid operand for instruction  
    sub x0, x0, #0xb0  
        ^  
and so on...  
  
And last but not least, thank you for this great library. Keep up the good work!

---

## Comment 1

> Username: gdochev  
> Created at: 2015-05-01 19:47:23 UTC  
> Updated at: 2015-05-01 19:48:48 UTC  
> Url: https://github.com/boostorg/context/issues/14#issuecomment-98217796  

My user-config.jam looks like this:  
  
  using darwin : 8.3~iphone  
  : /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang++ -arch arm64 -fvisibility=hidden -fvisibility-inlines-hidden -DBOOST_AC_USE_PTHREADS -DBOOST_SP_USE_PTHREADS -std=c++11 -stdlib=libc++  
  : <striper> <root>/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer  
  : <architecture>arm <target-os>iphone  
  ;  
  using darwin : 8.3~iphonesim  
  : /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang++ -arch i386 -arch x86_64 -fvisibility=hidden -fvisibility-inlines-hidden -DBOOST_AC_USE_PTHREADS -DBOOST_SP_USE_PTHREADS -std=c++11 -stdlib=libc++  
  : <striper> <root>/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer  
  : <architecture>x86 <target-os>iphone  
  ;

---

## Comment 2

> Username: olk  
> Created at: 2015-05-02 13:39:42 UTC  
> Url: https://github.com/boostorg/context/issues/14#issuecomment-98359933  

the code was reported to be working - strange

---

## Comment 3

> Username: olk  
> Created at: 2015-05-02 13:43:09 UTC  
> Url: https://github.com/boostorg/context/issues/14#issuecomment-98360529  

see - https://github.com/boostorg/context/pull/5

---

## Comment 4

> Username: gdochev  
> Created at: 2015-05-02 13:52:06 UTC  
> Url: https://github.com/boostorg/context/issues/14#issuecomment-98360817  

Hi Oliver, thank you for the quick response. I am no expert in arm or clang so I am mostly guessing here but it looks as though the assembly syntax in make_arm64_aapcs_macho_gas.S may not be digestible by the integrated arm assembler in clang (https://wiki.libav.org/Tools/gas-preprocessor)

---

## Comment 5

> Username: gdochev  
> Created at: 2015-06-04 14:47:30 UTC  
> Url: https://github.com/boostorg/context/issues/14#issuecomment-108921959  

Hi Oliver, do you have any updates on this?

---

## Comment 6

> Username: olk  
> Created at: 2015-12-26 14:50:44 UTC  
> Url: https://github.com/boostorg/context/issues/14#issuecomment-167329079  

I don't own an ARM64 running on iOS - I've merged suggested patches as you see here  
https://github.com/boostorg/context/pull/5.

---

## Comment 7

> Username: olk  
> Created at: 2016-10-11 08:11:25 UTC  
> Url: https://github.com/boostorg/context/issues/14#issuecomment-252842250  

should work now
