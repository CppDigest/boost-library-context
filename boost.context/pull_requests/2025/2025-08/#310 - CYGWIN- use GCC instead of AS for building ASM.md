# #310 [CMAKE] CYGWIN: use GCC instead of AS for building ASM [Open]

> Username: carlo-bramini  
> Created at: 2025-08-01 17:41:37 UTC  
> Updated at: 2025-08-06 06:02:21 UTC  
> Url: https://github.com/boostorg/context/pull/310  

Compiling on CYGWIN shows this error when building assembly sources:  
```  
/usr/bin/as: unrecognized option `-x'  
ninja: build stopped: subcommand failed.  
```  
CMakeLists.txt applies `-x assembler-with-cpp` but this option is expected to work only if `gcc` or `g++` drivers are called.  
Instead, during configuration, CMake prints this:  
```  
-- Found assembler: /usr/bin/as  
```  
But this is wrong, because `-x` option is not recognized by `as`.  
This happens because CMakeLists.txt forces ASM-ATT which excludes `gcc` and it uses `as` instead.  
But it does this ONLY for CYGWIN and actually it is not possible to understand why it has been done.  
The solution of the problem is to let CMake using `gcc` instead of `as`, by simply removing ASM-ATT and use ASM instead.  
After that, CMake prints this during the configuration:  
```  
-- Found assembler: /usr/bin/cc  
```  
and context is now built without problems after this patch.  
See also #309 for additional details.

---

## Comment 1

> Username: olk  
> Created_at: 2025-08-01 19:49:15 UTC  
> Url: https://github.com/boostorg/context/pull/310#issuecomment-3145610383  

did you run the unit-test?

---

## Comment 2

> Username: carlo-bramini  
> Created_at: 2025-08-02 12:03:05 UTC  
> Url: https://github.com/boostorg/context/pull/310#issuecomment-3146462400  

> did you run the unit-test?  
  
Actually not, I'm still not able to build all boost libraries because other errors.  
  
However, and only for CYGWIN, if you force to use `as` instead of `gcc` and you feed `-x assembler-with-cpp` on the command line, then it will never build the source code because that option is not recognized by the raw assembler.  
This is inevitable.

---

## Comment 3

> Username: olk  
> Created_at: 2025-08-06 06:02:21 UTC  
> Url: https://github.com/boostorg/context/pull/310#issuecomment-3157513053  

The PR will only be accepted if the tests are executed correctly.

---
