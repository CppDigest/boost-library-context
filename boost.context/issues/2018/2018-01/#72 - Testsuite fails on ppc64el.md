# #72 - Testsuite fails on ppc64el [Closed]

> Username: jcowgill  
> Created at: 2018-01-15 17:14:44 UTC  
> Updated at: 2019-10-01 11:30:27 UTC  
> Closed at: 2019-05-04 08:43:02 UTC  
> Url: https://github.com/boostorg/context/issues/72  

Running the testsuite on ppc64el fails with the following error (this is commit 93da162dd847f2f5ce3d30714e330c745b439b86).  
  
```  
testing.capture-output ../../../bin.v2/libs/context/test/test_callcc_asm.test/gcc-7/release/link-static/threadapi-pthread/threading-multi/test_callcc_asm.run  
====== BEGIN OUTPUT ======  
Running 11 test cases...  
unknown location(0): fatal error: in "Boost.Context: callcc test suite/_test_move": memory access violation at address: 0x00000000: no mapping at fault address  
test_callcc.cpp(147): last checkpoint  
  
*** 1 failure is detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
Running `fc` fails with:  
```  
testing.capture-output ../../../bin.v2/libs/context/test/test_fcontext.test/gcc-7/release/link-static/threadapi-pthread/threading-multi/test_fcontext.run  
====== BEGIN OUTPUT ======  
Running 12 test cases...  
f1: entered  
unknown location(0): fatal error: in "Boost.Context: context test suite/_test_ontop": memory access violation at address: 0x3fffa94c4fb8: invalid permissions  
test_fcontext.cpp(308): last checkpoint  
  
*** 1 failure is detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: jcowgill  
> Created at: 2018-01-15 17:17:06 UTC  
> Url: https://github.com/boostorg/context/issues/72#issuecomment-357743175  

For good measure, running `ec` fails with:  
```  
testing.capture-output ../../../bin.v2/libs/context/test/test_execution_context.test/gcc-7/release/link-static/threadapi-pthread/threading-multi/test_execution_context.run  
====== BEGIN OUTPUT ======                                                                                                                           
Running 13 test cases...                                                                                                                                                  
unknown location(0): fatal error: in "Boost.Context: execution_context v2 test suite/_test_move": signal: SIGSEGV, si_code: 2137250680 (memory access violation at address: 0x00000003)  
test_execution_context_v2.impl(236): last checkpoint                                                                                                                                                                 
                                                                      
*** 1 failure is detected in the test module "Master Test Suite"  
                                                
EXIT STATUS: 201                          
====== END OUTPUT ======  
```  
  
These are probably related to each other though.

---

## Comment 2

> Username: olk  
> Created at: 2018-01-16 04:51:46 UTC  
> Updated at: 2018-01-16 04:52:40 UTC  
> Url: https://github.com/boostorg/context/issues/72#issuecomment-357853299  

are you sure? commit 93da162 only increases the stacksize from 64kB to 128kB  
unfortunately I've no access to a PowerPC system - could you do a git bisect?

---

## Comment 3

> Username: jcowgill  
> Created at: 2018-01-17 15:43:54 UTC  
> Url: https://github.com/boostorg/context/issues/72#issuecomment-358344739  

```  
ba357205a9968bc500563ade2fcdf23fb44c86a8 is the first bad commit  
commit ba357205a9968bc500563ade2fcdf23fb44c86a8  
Author: Oliver Kowalke <oliver.kowalke@gmx.de>  
Date:   Fri Jan 1 22:03:18 2016 +0100  
  
    class captured_context added  
  
:040000 040000 b4bb4109325ab62d6883b7419696e448402f7cdc 7f59a322002f3ea4e1bbea173ea17ed4944b053f M      build  
:040000 040000 275318b7f5bde9eb6dad96a8716eb60ddaa427b4 b676c7626d165737a255e4e2180f1bbb35702068 M      example  
:040000 040000 4a8f5926ff811caff22f1b94e90689faedfe5b61 40bd1d1247ee099a55e631059e37b2a83ef613ab M      include  
:040000 040000 256f828e46469c6f05ae8fe16e0b638ad46ec751 2edb64774e556889028b694761102ad345205170 M      performance  
:040000 040000 41d05a56035bce91e32239cbf8e8a154c04777c7 38ecccbf5eedaf0a1df68692a3ad3361a4e6e7c9 M      src  
:040000 040000 4365311c6f6f708e4b3b6884e11980a0ad5d1ab0 527284969b74954db9d38af9ef677539092ced4f M      test  
```

---

## Comment 4

> Username: olk  
> Created at: 2018-01-17 16:19:22 UTC  
> Url: https://github.com/boostorg/context/issues/72#issuecomment-358357023  

ty, I'll take alook at it

---

## Comment 5

> Username: chfast  
> Created at: 2019-10-01 11:06:40 UTC  
> Url: https://github.com/boostorg/context/issues/72#issuecomment-536986461  

> unfortunately I've no access to a PowerPC system  
  
I am successfully using qemu to emulate PowerPC and other architectures in my CI setup. This also works inside docker containers (expect for multithreaded programs). Maybe this is applicable for testing boost.context. Let me know if I can help.

---

## Comment 6

> Username: olk  
> Created at: 2019-10-01 11:30:27 UTC  
> Url: https://github.com/boostorg/context/issues/72#issuecomment-536994019  

@chfast: could you run the unit test (run b2 in sub-directory test) at your PPC64 setup?
