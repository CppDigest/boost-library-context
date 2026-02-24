# #214 - boost 1.81 architecture=arm+x86 fails to match fcontext asm implementation [Closed]

> Username: uptycs-rmack  
> Created at: 2022-12-22 22:31:27 UTC  
> Updated at: 2023-01-26 05:52:33 UTC  
> Closed at: 2023-01-26 05:52:25 UTC  
> Url: https://github.com/boostorg/context/issues/214  

It looks like sometime recently boost changed the architecture flag for building fat binaries on Apple from "combined" to "arm+x86". That appears to me to break the auto-selection of the fcontext asm implementation. A patch such as below is needed to include both implementations as expected.  
  
```  
--- boost_1_81_0.orig/libs/context/build/Jamfile.v2     2022-12-07 20:02:41.000000000 -0500  
+++ boost_1_81_0/libs/context/build/Jamfile.v2  2022-12-22 17:03:41.000000000 -0500  
@@ -853,7 +853,7 @@  
      asm/jump_combined_sysv_macho_gas.S  
      asm/ontop_combined_sysv_macho_gas.S  
    : <abi>sysv  
-     <architecture>combined  
+     <architecture>arm+x86  
      <binary-format>mach-o  
    ;  
```

---

## Comment 1

> Username: olk  
> Created at: 2022-12-23 10:16:49 UTC  
> Url: https://github.com/boostorg/context/issues/214#issuecomment-1363816975  

ty - could you verify commit '5bcc1ab  architecture flag for Apple's combined ha changed to 'arm+x86'' please (I don't own Appple hardware).

---

## Comment 2

> Username: uptycs-rmack  
> Created at: 2022-12-26 11:45:39 UTC  
> Url: https://github.com/boostorg/context/issues/214#issuecomment-1365114534  

I applied the patch to boost 1.81 source tarball and built boost using the x86_64 build of Apple's compiler with the "-arch x86_64 -arch arm64" compiler flags to build a universal binary. I confirmed using lipo/nm that libboost_context-mt-a64.a contained the context-related objects for both Intel and Arm. I confirmed my application successfully linked a universal binary against this library. Patch looks good, thank you.

---

## Comment 3

> Username: olk  
> Created at: 2023-01-26 05:52:33 UTC  
> Url: https://github.com/boostorg/context/issues/214#issuecomment-1404588766  

ty
