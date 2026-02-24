# #92 Implement mips64 n64 elf [Merged]

> Username: FlyGoat  
> Created at: 2018-11-28 07:18:51 UTC  
> Updated at: 2021-07-02 01:44:20 UTC  
> Merged at: 2018-11-28 09:23:40 UTC  
> Closed at: 2018-11-28 09:23:41 UTC  
> Url: https://github.com/boostorg/context/pull/92  

n64 ABI is very different from o32 ABI.  
It has passed all the tests on Loongson-3B1500 with GCC-8.1.

---

## Comment 1

> Username: olk  
> Created_at: 2018-11-28 09:23:34 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-442377545  

very nice, ty

---

## Comment 2

> Username: olk  
> Created_at: 2018-11-28 09:25:07 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-442378042  

Did you execute the unit-tests (sub-dir `test`)?

---

## Comment 3

> Username: FlyGoat  
> Created_at: 2018-11-28 10:08:44 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-442391721  

Yes, that's what meant by test.

---

## Comment 4

> Username: neheb  
> Created_at: 2021-02-13 01:50:02 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-778542727  

@FlyGoat   
  
Any idea about this error?  
  
```  
ontop_mips64_n64_elf_gas.S:71: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f24,0($sp)'  
ontop_mips64_n64_elf_gas.S:72: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f25,8($sp)'  
ontop_mips64_n64_elf_gas.S:73: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f26,16($sp)'  
ontop_mips64_n64_elf_gas.S:74: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f27,24($sp)'  
ontop_mips64_n64_elf_gas.S:75: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f28,32($sp)'  
ontop_mips64_n64_elf_gas.S:76: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f29,40($sp)'  
ontop_mips64_n64_elf_gas.S:77: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f30,48($sp)'  
ontop_mips64_n64_elf_gas.S:78: Error: opcode not supported on this processor: octeon+ (mips64r2) `s.d $f31,56($sp)'  
ontop_mips64_n64_elf_gas.S:86: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f24,0($sp)'  
ontop_mips64_n64_elf_gas.S:87: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f25,8($sp)'  
ontop_mips64_n64_elf_gas.S:88: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f26,16($sp)'  
ontop_mips64_n64_elf_gas.S:89: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f27,24($sp)'  
ontop_mips64_n64_elf_gas.S:90: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f28,32($sp)'  
ontop_mips64_n64_elf_gas.S:91: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f29,40($sp)'  
ontop_mips64_n64_elf_gas.S:92: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f30,48($sp)'  
ontop_mips64_n64_elf_gas.S:93: Error: opcode not supported on this processor: octeon+ (mips64r2) `l.d $f31,56($sp)'  
```

---

## Comment 5

> Username: neheb  
> Created_at: 2021-02-13 02:04:58 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-778545051  

missing hard float define...

---

## Comment 6

> Username: FlyGoat  
> Created_at: 2021-02-13 02:56:46 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-778551496  

Will add that define.  
Btw I thought MIPS64 mandatory requires hard float.

---

## Comment 7

> Username: neheb  
> Created_at: 2021-02-13 03:01:18 UTC  
> Updated_at: 2021-02-13 03:04:36 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-778552067  

The Cavium Octeon line of processors is MIPS64 with no FPU. I can confirm later with someone that has the hardware.  
  
edit: ah OK. Octeon II has no FPU. Octeon III does: https://wiki.debian.org/MIPSPort

---

## Comment 8

> Username: neheb  
> Created_at: 2021-02-13 03:01:35 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-778552108  

I added https://github.com/boostorg/context/pull/159

---

## Comment 9

> Username: FlyGoat  
> Created_at: 2021-02-13 05:40:50 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-778567629  

> I added #159  
  
Thanks!

---

## Comment 10

> Username: brad0  
> Created_at: 2021-07-02 01:44:08 UTC  
> Updated_at: 2021-07-02 01:44:19 UTC  
> Url: https://github.com/boostorg/context/pull/92#issuecomment-872652665  

BTW trying to build on OpenBSD/mips64 it fails.. https://github.com/boostorg/context/issues/175

---
