# #422 check -> hana_check. If a "check" target exists, give it a dependency on [Merged]

> Username: tzlaine  
> Created at: 2018-09-20 00:53:00 UTC  
> Updated at: 2018-09-25 12:51:34 UTC  
> Merged at: 2018-09-20 18:47:24 UTC  
> Closed at: 2018-09-20 18:47:24 UTC  
> Url: https://github.com/boostorg/hana/pull/422  

hana_check. Add a custom target check that depends on hana_check otherwise.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2018-09-20 00:53:19 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423004133  

Following the discussion on the boost-dev mailing list.

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-09-20 21:12:20 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423335000  

I'm not sure that this is correct. If there's a top-level `check` target, it will already have been defined to invoke `ctest`. So now we get two targets that both call `ctest`, one a dependency of the other. Doesn't quite ring true to me, although I may be missing something.

---

## Comment 3

> Username: ldionne  
> Created_at: 2018-09-21 15:12:08 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423567618  

Good point, but what doesn’t sound right is that the top level target would call ctest. It seems like the top level target should not do anything and should only be used to add a dependency on it. Does that make sense?

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-09-21 15:32:11 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423575146  

No, it doesn't. Consider what happens when three libraries do what Hana does now. Even if the top-level check target does nothing, the three foo-check dependent targets would still call ctest three times.  
  
If one wants to support `cmake --build . --target hana-check` from the top level, this should probably call ctest, but it needs to supply a test filter and not be a dependency on the top-level `check`.  
  
Not sure what's the best (or idiomatic) way to approach this.

---

## Comment 5

> Username: tzlaine  
> Created_at: 2018-09-21 18:03:13 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423623434  

It seems like the "hana_check" target is already only going to do the Hana tests.  It's running in the Hana test/ working dir.  The question then becomes whether the top-level check target does too.  So if we want to leave room for a "check" target at the top level, we should make sure it doesn't call ctest from the top level, unfiltered.

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-09-21 18:26:45 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423630209  

It's a matter of workflow then. If we suppose, say, 130 Boost libraries, should the top-level `check` call 130 ctests, each in libs/libname/test, or should all these tests be added to `check` and run with a single ctest call?  
  
It seems to me that in the first case a failing ctest for lib4 will abort the whole test run (unless -k or something.)

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-09-21 18:42:38 UTC  
> Updated_at: 2018-09-21 18:44:28 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423634604  

There's another use case that doesn't seem to be not handled properly by this arrangement. Suppose you have  
  
```  
myproject/  
  CMakeLists.txt  
  mysubproject1/  
  mysubproject2/  
  deps/  
    hana/  
    dep2/  
```  
  
and `myproject` uses `add_subdirectory` for its subprojects and its dependencies.  
  
Now the author of `myproject` wants `cmake --build . --target check` to run only the tests for his own projects but not for the dependencies. Which workflow is also a matter of debate; some say that he should filter the tests. I however think that it's more natural to do  
  
```  
add_subdirectory(deps/hana)  
add_subdirectory(deps/dep2)  
  
enable_testing() # test everything below, but not above  
  
add_subdirectory(mysubproject1)  
add_subdirectory(mysubproject2)  
```  
  
For this to work, the CMakeLists files of the subprojects must not do anything global when not invoked as top level. In particular, they should not `enable_testing()` themselves, and should not add dependencies to the global `check` target.  
  
This style doesn't appear very popular though, because few of the CMakeLists.txt files I've seen support such an use (delegating every decision to the superproject.)

---

## Comment 8

> Username: tzlaine  
> Created_at: 2018-09-21 18:54:44 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-423637953  

That seems reasonable to me.  I've never moved enable_testing() to accomplish this, but I've done the equivalent thing in every project that includes Google Test or Google Benchmark, so I don't repeatedly run their tests.

---

## Comment 9

> Username: ldionne  
> Created_at: 2018-09-24 22:07:38 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-424141698  

TBH, my (limited) experience with nesting CMake projects has been negative so far, and I would be delighted if we didn't have to do this for Boost. The problem is specifically that one starts having to be paranoid about what is defined and how things are done, because the super project or sibling projects could conflict. One also stops being able to use builtin CMake concepts like `BUILD_SHARED_LIBS` because that is too coarse grained when applied to many projects -- instead, it is necessary to define a `<PROJECT>_BUILD_SHARED_LIBS` variable (or similar) to configure this per-project.  
  
This is just one example, but I've witnessed these downsides while working on LLVM, which uses this paradigm. If Boost can avoid that, I believe the CMake files for individual libraries would be simpler.

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-09-24 22:27:48 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-424146318  

Many share your experience, which is why the currently recommended "right way" to use CMake is to export and import to achieve isolation, instead of using add_subdirectory to achieve consistency (ABI-wise and otherwise.)  
  
I don't agree with this defeatist position.  
  
In my opinion a properly written CMake file absolutely needs to support clean add_subdirectory use. I'm not yet as certain how this needs to be done, but at the moment I tend towards the approach of just disabling everything controversial (find_package, including CTest, otherwise changing global settings, running a doc build, running tests, installing) when not top level, leaving just the target descriptions.  
  
Fitting into a superproject such as Boost that requires cooperation will be harder, but we can cross that bridge when we come to it.

---

## Comment 11

> Username: ldionne  
> Created_at: 2018-09-25 03:37:58 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-424197076  

> Many share your experience, which is why the currently recommended "right way" to use CMake is to export and import to achieve isolation, instead of using add_subdirectory to achieve consistency (ABI-wise and otherwise.)  
  
How does using `add_subdirectory` help you achieve ABI consistency? You can still add options that could conceivably impact the ABI on a per-target basis.

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-09-25 12:51:34 UTC  
> Url: https://github.com/boostorg/hana/pull/422#issuecomment-424330296  

You can do many things to break add_subdirectory use. :-)

---
