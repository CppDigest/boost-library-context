# #473 - Failing tests with gcc 6.3.0 [Closed]

> Username: vissarion  
> Created at: 2018-04-12 13:43:58 UTC  
> Updated at: 2018-11-26 18:28:54 UTC  
> Closed at: 2018-11-26 18:28:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/473  

The following tests are failing both in Boost ```1.67.0``` and in ```develop``` branch with ```gcc 6.3.0```  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test/algorithms/overlay/test_get_turns.hpp(214): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) and LINESTRING(4 5,3 2,1 0,0 0) -> Expected turns #: 3 detected turns #: 4  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) and LINESTRING(4 5,3 2,1 0,0 0) -> Expected turns: ecc== mix+= txi=+ Detected turns: mix+= mui=+ tui=+ txu++   
test/algorithms/overlay/test_get_turns.hpp(214): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(4 5,3 2,1 0,0 0) and LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) -> Expected turns #: 3 detected turns #: 4  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(4 5,3 2,1 0,0 0) and LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) -> Expected turns: ecc== mxi=+ tix+= Detected turns: miu+= mxi=+ tiu+= tux++   
  
*** 4 failures are detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2018-04-12 13:51:29 UTC  
> Updated at: 2018-04-12 16:29:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/473#issuecomment-380812306  

Curious, what is the build `variant` used?  
  
Here, https://circleci.com/workflow-run/42b255d9-5f6b-4023-9965-c52d605a811e, it's like 15 or 18 versions of compilers build Boost.GIL tests and just one is failing - with GCC 5 `variant=release address-model=64`. It also is failing with VS2017, same build config as for GCC.  
Likely, there's a nasty undefined behaviour bug in GIL due to unexpected integer promotions/conversions and bitwise shuffling. Fun :)

---

## Comment 2

> Username: vissarion  
> Created at: 2018-04-12 14:10:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/473#issuecomment-380818714  

It is failing with both ```variant=release``` and ```variant=debug```.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-04-12 16:12:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/473#issuecomment-380860607  

I don't think this is caused by a bug in a compiler. Various versions of GCC and clang give the same result. This may be caused by difference in calculation of some internals in various compilers. Both results may be acceptable. This needs more investigation to be sure.

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-11-26 18:28:17 UTC  
> Updated at: 2018-11-26 18:28:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/473#issuecomment-441745828  

This is now "fixed" by testing it only for `double` coordinates.
