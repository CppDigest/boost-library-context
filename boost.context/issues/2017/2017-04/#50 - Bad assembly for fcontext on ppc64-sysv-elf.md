# #50 - Bad assembly for fcontext on ppc64/sysv/elf [Closed]

> Username: DaoWen  
> Created at: 2017-04-05 02:34:22 UTC  
> Updated at: 2017-04-07 06:34:57 UTC  
> Closed at: 2017-04-05 04:38:12 UTC  
> Url: https://github.com/boostorg/context/issues/50  

This past week I was testing some code (which uses Boost.Context routines) on a POWER7 RHEL system. I had hundreds of successful test runs on x86_64 machines, but I was getting segfaults everywhere on the ppc64 system. I traced the problems back to the machine code for the `fcontext` functions, where I identified several bugs in the assembly routines:  
  
  1. Saving `r29` into `r28`'s spot (typo).  
  2. Assuming identical ABI for returning `transfer_t` struct and passing the struct as the first argument to a function.  
  3. No distinction between ELF v1 and v2 in the `ontop_fcontext` when jumping into the target function.  
  
These bugs were introduced in v1.61, which is when the internal `fcontext` interface was updated. I didn't have any problems previously when using v1.54. I've patched the `ppc64_sysv_elf_gas` versions of the `fcontext` functions, and I'll open a pull request shortly; however, the same bugs are also present in the code for other ppc targets.  
  
I was surprised to find so many bugs in the code. When I ran the unit tests on the POWER7 machine, they failed consistently. This leads me to think this code was not actually tested. I understand that the library maintainers might not have the resources to test the code on all of the "supported" platforms; however, maintaining some sort of a testing matrix in the documentation listing which versions have been verified would be useful. Adding an obvious warning to check the verification status for platforms with lower-priority support would also be helpful.

---

## Comment 1

> Username: olk  
> Created at: 2017-04-05 04:38:12 UTC  
> Url: https://github.com/boostorg/context/issues/50#issuecomment-291752072  

I've no access to to POWER systems - so I can't test the code. So I've to wait for bug reports.  
Unfortunately sometimes I get false success replies from users.  
  
ty for the fix

---

## Comment 2

> Username: olk  
> Created at: 2017-04-05 19:17:51 UTC  
> Updated at: 2017-04-05 19:18:21 UTC  
> Url: https://github.com/boostorg/context/issues/50#issuecomment-291967272  

Could you fetch the latest sources and run the unit-tests for PPC32 + PPC64 please.  
I've changed the unit-test (snprintf()).

---

## Comment 3

> Username: DaoWen  
> Created at: 2017-04-06 05:33:25 UTC  
> Url: https://github.com/boostorg/context/issues/50#issuecomment-292073317  

Yes, I should be able to make some time to do that tomorrow.

---

## Comment 4

> Username: DaoWen  
> Created at: 2017-04-07 06:33:59 UTC  
> Url: https://github.com/boostorg/context/issues/50#issuecomment-292453575  

I tested the latest sources with ppc64/sysv/elfv1, and as far as I can tell all of the tests are passing. Unfortunately, I don't have the dependencies installed to build 32-bit applications with GCC. I'll see if I can get that set up later and then test the 32-bit version.

---

## Comment 5

> Username: olk  
> Created at: 2017-04-07 06:34:57 UTC  
> Url: https://github.com/boostorg/context/issues/50#issuecomment-292453748  

thank you - that's really nice
