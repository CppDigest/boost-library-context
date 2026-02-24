# #89 - Boost v1.67 compile error for MIPS n32 loongson2f CPU [Closed]

> Username: emtone  
> Created at: 2018-11-06 14:08:05 UTC  
> Updated at: 2020-03-23 15:18:43 UTC  
> Closed at: 2020-03-23 15:18:43 UTC  
> Url: https://github.com/boostorg/predef/issues/89  

Hello,  
I use gentoo on Lemote Yeeloong 8101 notebook which has a MIPS n32 loongson2f CPU. It's OK when I compile without "context" USE flag. Now I want to use KiCad on this computer which need boost recompile with "contest" USE flag:  
`[ebuild U *] dev-libs/boost-1.67.0:0/1.67.0::gentoo [1.65.0:0/1.65.0::gentoo] USE="context* nls python threads -debug -doc -icu (-mpi) -static-libs -tools" ABI_MIPS="(n32) -n64 -o32" PYTHON_TARGETS="python2_7 python3_6 -python3_4 -python3_5 -python3_7%" 0 KiB`  
When I compile with "context" USE flag, An error has occurred:  
```  
**/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)**  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
error: No best alternative for libs/context/build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
```  
It looks like predef.jam error.

---

## Comment 1

> Username: emtone  
> Created at: 2018-11-06 14:09:25 UTC  
> Url: https://github.com/boostorg/predef/issues/89#issuecomment-436265007  

And can't get the _**default architecture**_  
```  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : none (cached)  
    - symlinks supported       : yes (cached)  
  
```

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-03-01 14:21:22 UTC  
> Url: https://github.com/boostorg/predef/issues/89#issuecomment-593102588  

Sorry for the very late response.. What command did you use for building that you get this error?

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-03-23 15:18:43 UTC  
> Url: https://github.com/boostorg/predef/issues/89#issuecomment-602667804  

Closing as inactive/no-repro.
