# #208 - s390x with Clang [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-10-04 01:13:12 UTC  
> Updated at: 2022-10-17 13:25:26 UTC  
> Closed at: 2022-10-17 13:25:26 UTC  
> Url: https://github.com/boostorg/context/issues/208  

Is s390x not supported on clang on purpose? It's missing a simple target declaration from what I can see.   
  
It's currently a blocker on boostorg/beast#2389, though we can of course disable clang if clang is unsupported.

---

## Comment 1

> Username: olk  
> Created at: 2022-10-04 05:03:14 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1266401463  

I don't have access to an s390 system ... could you verify that branch develop-#208 will fix your issue.

---

## Comment 2

> Username: sdarwin  
> Created at: 2022-10-04 20:53:35 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1267567037  

develop-208 seems to be a major improvement.  Before, the build failed with 472 errors.  Now, only one or two errors.  https://drone.cpp.al/samd2/beast/244/1/2  
  
```  
...failed clang-linux.compile.asm bin.v2/libs/context/build/clang-linux-12/release/threading-multi/visibility-hidden/asm/make_s390x_sysv_elf_gas.o...  
  
clang-linux.compile.asm bin.v2/libs/context/build/clang-linux-12/release/threading-multi/visibility-hidden/asm/jump_s390x_sysv_elf_gas.o  
  
libs/context/src/asm/jump_s390x_sysv_elf_gas.S:80:2: error: unknown directive  
  
 .machine "z10"  
```

---

## Comment 3

> Username: sdarwin  
> Created at: 2022-10-04 21:03:28 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1267576062  

> I don't have access to an s390 system  
  
cloud.ibm.com now has on-demand s390x virtual machines. You can launch an instance for an hour, or a day, and then delete it.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-10-10 17:10:14 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1273605166  

That's what we would need for the build, yes. Can you merge this and we'll see if it solves our dependency issue?

---

## Comment 5

> Username: sdarwin  
> Created at: 2022-10-11 19:07:12 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1275149924  

Those one or two errors seem to be solved by changing from clang-12 to clang-14.  The fix in boostorg/context was needed though.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2022-10-12 00:47:49 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1275443026  

@olk Can you merge `develop-#208` ?

---

## Comment 7

> Username: olk  
> Created at: 2022-10-16 05:28:30 UTC  
> Updated at: 2022-10-16 05:28:40 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1279895486  

commit pushed - please verify

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2022-10-17 13:25:26 UTC  
> Url: https://github.com/boostorg/context/issues/208#issuecomment-1280857271  

Thanks Oliver, that fixed it for us.
