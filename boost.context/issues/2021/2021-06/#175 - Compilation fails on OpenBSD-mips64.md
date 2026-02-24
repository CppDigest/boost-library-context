# #175 - Compilation fails on OpenBSD/mips64 [Closed]

> Username: brad0  
> Created at: 2021-06-27 06:07:07 UTC  
> Updated at: 2021-07-23 06:34:10 UTC  
> Closed at: 2021-07-23 06:34:10 UTC  
> Url: https://github.com/boostorg/context/issues/175  

Trying to build Boost 1.73 on OpenBSD/mips64 it fails to link the context library as shown..  
  
```  
    "c++"   -o "bin.v2/libs/context/build/clang-linux-11.1.0/release/pch-off/threading-multi/visibility-hidden/libboost_context-mt.so.14.0" -Wl,-soname -Wl,libboost_context-mt.so.14.0 -shared -Wl,--start-group "bin.v2/libs/context/build/clang-linux-11.1.0/release/pch-off/threading-multi/visibility-hidden/asm/make_mips64_n64_elf_gas.o" "bin.v2/libs/context/build/clang-linux-11.1.0/release/pch-off/threading-multi/visibility-hidden/asm/jump_mips64_n64_elf_gas.o" "bin.v2/libs/context/build/clang-linux-11.1.0/release/pch-off/threading-multi/visibility-hidden/asm/ontop_mips64_n64_elf_gas.o" "bin.v2/libs/context/build/clang-linux-11.1.0/release/pch-off/threading-multi/visibility-hidden/posix/stack_traits.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -pthread -fvisibility=hidden -fvisibility-inlines-hidden   
  
/usr/bin/ld: bin.v2/libs/context/build/clang-linux-11.1.0/release/pch-off/threading-multi/visibility-hidden/asm/make_mips64_n64_elf_gas.o: CALL16 reloc at 0x14 not against global symbol  
bin.v2/libs/context/build/clang-linux-11.1.0/release/pch-off/threading-multi/visibility-hidden/asm/make_mips64_n64_elf_gas.o: could not read symbols: Bad value  
c++: error: linker command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 1

> Username: olk  
> Created at: 2021-06-27 07:54:39 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-869118692  

I do not have access to a MIPS64 system so I can't debug/test it (code was contributed by another user) ... sorry.

---

## Comment 2

> Username: neheb  
> Created at: 2021-07-02 03:01:55 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-872676864  

I'm guessing this is clang+lld. There's a comment here:  
  
https://github.com/boostorg/context/blob/develop/src/asm/make_mips64_n64_elf_gas.S#L86

---

## Comment 3

> Username: brad0  
> Created at: 2021-07-02 03:07:05 UTC  
> Updated at: 2021-07-02 03:07:17 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-872678819  

> I'm guessing this is clang+lld. There's a comment here:  
  
We're currently utilizing clang + ld.bfd. We have been slowly converting to lld but powerpc and mips64 are still to go.

---

## Comment 4

> Username: neheb  
> Created at: 2021-07-02 03:07:14 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-872678870  

wait a minute... are you building with mips16?

---

## Comment 5

> Username: brad0  
> Created at: 2021-07-02 03:08:01 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-872679150  

> wait a minute... are you building with mips16?  
  
Hrmm? Only mips64.

---

## Comment 6

> Username: neheb  
> Created at: 2021-07-02 03:16:41 UTC  
> Updated at: 2021-07-02 03:21:35 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-872682169  

Maybe patch out the entries in build/Jamfile.v2 .  
  
edit: could also be a PIC problem. Who knows.

---

## Comment 7

> Username: FlyGoat  
> Created at: 2021-07-02 09:12:54 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-872847924  

Could you please help take a look at which symbol it is calling? I have no clues.

---

## Comment 8

> Username: brad0  
> Created at: 2021-07-23 06:34:10 UTC  
> Url: https://github.com/boostorg/context/issues/175#issuecomment-885431787  

It looks like with me pushing an update to Boost 1.76 that context now builds Ok on mips64.
