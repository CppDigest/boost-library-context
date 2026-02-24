# #297 - BOOST 1.87 (And 1.88.0 beta1) Context: Removed support for building with ucontext? [Closed]

> Username: flarren1  
> Created at: 2025-03-18 12:20:40 UTC  
> Updated at: 2025-05-25 14:36:09 UTC  
> Closed at: 2025-05-25 14:36:09 UTC  
> Url: https://github.com/boostorg/context/issues/297  

Hi,  
  
I am currently using boost 1.84 in an application were I use boost context + boost fibers to achieve userland threading. This project also uses gcc sanitizers to check for vulnerabilities. Therefore I follow the instructions provided:  
 "Sanitizers (GCC/Clang) are confused by the stack switches. The library (and Boost.Context too) is required to be compiled with property (b2 command-line) context-impl=ucontext and compilers santizer options. Users must define BOOST_USE_ASAN before including any Boost.Context headers when linking against Boost binaries" (https://live.boost.org/doc/libs/1_87_0/libs/fiber/doc/html/fiber/stack/sanitizers.html)  
  
  
This works fine for 1.84, but after trying to uplift to boost 1.87 i get the following issue:  
  
./b2 -j8 define=BOOST_USE_UCONTEXT context-impl=ucontext  
xxx/boost_1_87_0/tools/build/src/build/feature.jam:327: in validate-feature from module feature  
error: unknown feature context-impl  
xxx/boost_1_87_0/tools/build/src/build/feature.jam:361: in expand-subfeatures-aux from module feature  
xxx/boost_1_87_0/tools/build/src/build/feature.jam:422: in feature.expand-subfeatures from module feature  
xxx/ in apply-to-property-set from module build-request  
(builtin):-1: in sequence.transform from module sequence  
xxx/boost_1_87_0/tools/build/src/build/build-request.jam:32: in build-request.expand-no-defaults from module build-request  
xxx/boost_1_87_0/tools/build/src/build-system.jam:778: in module scope from module build-system  
  
The same command worked fine with 1.84 and I cannot see any differences in the documentation that says the feature flag has changed.  
Any ideas?  
  
Thanks and best regards  
Anders

---

## Comment 1

> Username: flarren1  
> Created at: 2025-03-21 09:36:08 UTC  
> Url: https://github.com/boostorg/context/issues/297#issuecomment-2748506246  

I tried boost 1.88.0.beta1 and unfortunately the issue seems to remain there.  
@olk Do you happen to know anything about removal of support for building with ucontext?

---

## Comment 2

> Username: flarren1  
> Created at: 2025-03-24 08:12:30 UTC  
> Url: https://github.com/boostorg/context/issues/297#issuecomment-2748506250  

The issue seems to be that the context impl-feature was not migrated from **libs/context/build/Jamfile.v2**  to  
**libs/context/boost-context-features.jam**. This file does not exist in 1.84.  
  
Other features like valgrind and segmented-stacks were already defined in boost-context-features.jam. Not sure if this was intentional or not. After adding context-impl to the features, it builds.

---

## Comment 3

> Username: flarren1  
> Created at: 2025-03-24 08:42:56 UTC  
> Url: https://github.com/boostorg/context/issues/297#issuecomment-2748506261  

What I describe above is already corrected by @grisumbras here https://github.com/boostorg/context/commit/12ac945158ae3c2373ec0c888899373218aa209f  
  
However, it is not corrected in the offical release 1.87, nor in 1.88 beta1. Does anyone know the way of working here? I am new to the community, sorry :)

---

## Comment 4

> Username: flarren1  
> Created at: 2025-03-27 06:42:11 UTC  
> Updated at: 2025-03-27 07:01:17 UTC  
> Url: https://github.com/boostorg/context/issues/297#issuecomment-2756906902  

I saw the mailing notification regarding freeze for 1.88: "Reminder: Master branch closes for the 1.88.0 release NEXT WEDNESDAY (2th April)"  
  
The problem is already corrected on the develop branch  in boost/context as far as I can see, however it is not included if you download the 1.88 beta1 package  
  
I am happy to help out if needed, but as mentioned earlier I do not know exactly what is needed to be done. Is it a matter of updating the context submodule in the top repo?
