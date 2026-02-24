# #365 - Duplicate symbols when including extensions/gis/projections/factory.hpp in more than one file [Closed]

> Username: kaoskorobase  
> Created at: 2016-10-18 11:42:27 UTC  
> Updated at: 2016-11-09 09:42:52 UTC  
> Closed at: 2016-11-09 09:21:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/365  

Including `extensions/gis/projections/factory.hpp` in more than one file leads to duplicate symbol linker errors (with clang on OSX):  
  
```  
duplicate symbol __ZN5boost8geometry11projections6detail7healpix8in_imageEddiii in:  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/Geometry.cpp.rel.o  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/LocationProcessor.cpp.rel.o  
duplicate symbol __ZN5boost8geometry11projections6detail7healpix7pj_signEd in:  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/Geometry.cpp.rel.o  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/LocationProcessor.cpp.rel.o  
duplicate symbol __ZN5boost8geometry11projections6detail7healpix22healpix_sphere_inverseERKdS5_RdS6_ in:  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/Geometry.cpp.rel.o  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/LocationProcessor.cpp.rel.o  
duplicate symbol __ZN5boost8geometry11projections6detail7healpix14healpix_sphereERKdS5_RdS6_ in:  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/Geometry.cpp.rel.o  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/LocationProcessor.cpp.rel.o  
duplicate symbol __ZN5boost8geometry11projections6detail7healpix3rotE in:  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/Geometry.cpp.rel.o  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/LocationProcessor.cpp.rel.o  
duplicate symbol __ZN5boost8geometry11projections6detail4isea13icostrianglesE in:  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/Geometry.cpp.rel.o  
    build/debug/macosx/x86_64/liblocosonic_dylib_obj/src/LocationProcessor.cpp.rel.o  
ld: 6 duplicate symbols for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
The culprits are non-inline functions and static namespace members in  
- `extensions/gis/projections/proj/healpix.hpp` and  
- `extensions/gis/projections/proj/isea.hpp`  
  
Commenting out these includes and the corresponding `_init` calls in `extensions/gis/projections/factory.hpp` fixes the linker errors.  
  
Since these files are automatically generated, I'm not sure where to fix this.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2016-11-09 09:21:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/365#issuecomment-259368003  

Thanks for the report! I fixed it in the generated sources - have to do the generator later. The fixes will be visible as changes when generating/committing generated updates.

---

## Comment 2

> Username: kaoskorobase  
> Created at: 2016-11-09 09:42:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/365#issuecomment-259372280  

Great, thanks!
