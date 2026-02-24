# #730 - Missing <arch-and-model> if using msvc [Open]

> Username: UMU618  
> Created at: 2021-07-10 15:40:33 UTC  
> Updated at: 2021-07-12 01:43:22 UTC  
> Url: https://github.com/boostorg/build/issues/730  

I use b2 to compile a simple example, and got an error with msvc/Windows:  
  
```  
$ b2  
...patience...  
...found 817 targets...  
...updating 1 target...  
msvc.link bin\msvc-14.2\release\cxxstd-latest-iso\threading-multi\test_boost.exe  
LINK : fatal error LNK1181: cannot open input file 'boost_program_options-vc142-mt-1_76.lib'  
  
        call "bin\standalone\msvc\msvc-14.2\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO "bin\msvc-14.2\release\cxxstd-latest-iso\threading-multi\main.obj"    "boost_program_options-vc142-mt-1_76.lib"  /MACHINE:X64 /MANIFEST:EMBED /subsystem:console /out:"bin\msvc-14.2\release\cxxstd-latest-iso\threading-multi\test_boost.exe" /LIBPATH:"C:\dev\lib"  
  
...failed msvc.link bin\msvc-14.2\release\cxxstd-latest-iso\threading-multi\test_boost.exe...  
...failed updating 1 target...  
```  
  
The file `boost_program_options-vc142-mt-1_76.lib` should be `boost_program_options-vc142-mt-x64-1_76.lib`.  
  
https://github.com/boostorg/build/blob/d52f1993d906aa3fd3649a8dd5134402794bf86e/src/contrib/boost.jam#L306  
  
After looking up `boost.jam`, I think on Windows this should be:  
  
```  
rule tag_versioned ( name : type ? : property-set )  
{  
    return [ common.format-name <base> <toolset> <threading> <runtime> <arch-and-model>  
        -$(.version_tag) -$(.build_id) : $(name) : $(type) : $(property-set) ] ;  
}  
```  
  
After adding <arch-and-model>, `b2 architecture=x86 address-model=64` succeeded.

---

## Comment 1

> Username: UMU618  
> Created at: 2021-07-10 15:45:57 UTC  
> Url: https://github.com/boostorg/build/issues/730#issuecomment-877658382  

Jamfile.v2  
  
```  
import boost ;  
  
# no problem if using <root> to locate $(BOOST_ROOT)  
#using boost : 1.76 : <root>C:/dev/boost_1_76_0 ;  
  
# failed if use the installed one  
using boost : 1.76 : <include>C:/dev/include/boost-1_76 <library>C:/dev/lib ;  
  
boost.use-project ;  
  
project test_boost  
  : requirements  
    <cxxstd>latest  
      <toolset>clang:<cxxstd>17  
    <optimization>speed  
    <threading>multi  
  : default-build release  
  : build-dir ./bin  
  ;  
  
exe test_boost  
  : main.cpp  
  : <library>/boost//program_options  
  ;  
```

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-07-11 20:33:03 UTC  
> Url: https://github.com/boostorg/build/issues/730#issuecomment-877856941  

https://github.com/boostorg/boost/commit/06c1e1ee6cde5231eda696b8d7bf957fd1333d93 was not mirrored to `src/contrib/boost.jam`

---

## Comment 3

> Username: UMU618  
> Created at: 2021-07-12 01:43:22 UTC  
> Url: https://github.com/boostorg/build/issues/730#issuecomment-877909430  

> [boostorg/boost@06c1e1e](https://github.com/boostorg/boost/commit/06c1e1ee6cde5231eda696b8d7bf957fd1333d93) was not mirrored to `src/contrib/boost.jam`  
  
Thank you! I will use `stage` instead of `install` on Windows.
