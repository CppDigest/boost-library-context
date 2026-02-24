# #515 - Incorrect LIBPATH for address-model=32,64 on Windows using libjpeg.jam libpng.jam libtiff.jam zlib.jam [Closed]

> Username: mloskot  
> Created at: 2019-12-17 17:48:38 UTC  
> Updated at: 2020-09-02 02:52:46 UTC  
> Closed at: 2020-09-02 02:52:46 UTC  
> Url: https://github.com/boostorg/build/issues/515  

I'm building Boost.GIL tests on Windows which depend on third-parties like libjpeg, libpng, etc.  
  
I've configured `user-config.jam` to consume those dependencies installed by vcpkg package manager.  
  
When requesting multiple architectures using `address-model=32,64` or `address-model=64,32`, targets for the second one fail to link due to incorrect `LIBPATH` in B2 command line.  
IOW, requesting single architecture  with `b2.exe address-model=64` or `b2.exe address-model=32` works fine.  
  
Initially, I posted the problem to https://lists.boost.org/boost-build/2019/12/30275.php, but following brief investigation on Slack with @pdimov, there seem to be a problem in B2.  
  
### `%HOME%\user-config.jam`  
  
```  
project  
  : requirements  
    <address-model>32:<dll-path>C:/vcpkg/installed/x86-windows/bin  
    <address-model>64:<dll-path>C:/vcpkg/installed/x64-windows/bin  
  ;  
  
using libjpeg  
  :  
  : <include>C:/vcpkg/installed/x86-windows/include <search>C:/vcpkg/installed/x86-windows/lib  
  : <address-model>32  
  ;  
  
using libjpeg  
  :  
  : <include>C:/vcpkg/installed/x64-windows/include <search>C:/vcpkg/installed/x64-windows/lib  
  : <address-model>64  
  ;  
  
using libpng  
  :  
  : <include>C:/vcpkg/installed/x86-windows/include <search>C:/vcpkg/installed/x86-windows/lib  
  : <address-model>32  
  ;  
  
using libpng  
  :  
  : <include>C:/vcpkg/installed/x64-windows/include <search>C:/vcpkg/installed/x64-windows/lib  
  : <address-model>64  
  ;  
  
using libtiff  
  :  
  : <include>C:/vcpkg/installed/x86-windows/include <search>C:/vcpkg/installed/x86-windows/lib  
  : <address-model>32  
  ;  
  
using libtiff  
  :  
  : <include>C:/vcpkg/installed/x64-windows/include <search>C:/vcpkg/installed/x64-windows/lib  
  : <address-model>64  
  ;  
  
using zlib  
  :  
  : <include>C:/vcpkg/installed/x86-windows/include <search>C:/vcpkg/installed/x86-windows/lib  
  : <address-model>32  
  ;  
  
using zlib  
  :  
  : <include>C:/vcpkg/installed/x64-windows/include <search>C:/vcpkg/installed/x64-windows/lib  
  : <address-model>64  
  ;  
```  
  
### Problem 1: `address-model=32,64` linking 64-bit target against 32-bit libraries  
  
    ```console  
    cd D:\boost.win  
    D:\boost.win> rmdir /s /q bin.v2  
    D:\boost.win> b2.exe toolset=msvc-14.2 address-model=32,64 libs/gil/test/extension/io//simple  
  
    Performing configuration checks  
  
        - default address-model    : 32-bit  
        - default architecture     : x86  
        - symlinks supported       : no  
        - junctions supported      : yes  
        - hardlinks supported      : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - BOOST_COMP_GNUC >= 4.3.0 : no  
        - libjpeg                  : yes  
        - zlib                     : yes  
        - libpng                   : yes  
        - libtiff                  : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - BOOST_COMP_GNUC >= 4.3.0 : no  
        - libjpeg                  : yes  
        - zlib                     : yes  
        - libpng                   : yes  
        - libtiff                  : yes  
    ...  
    C:\vcpkg\installed\x86-windows\lib\jpeg.lib : warning LNK4272: library machine type 'x86' conflicts with target machine type 'x64'  
    C:\vcpkg\installed\x86-windows\lib\zlib.lib : warning LNK4272: library machine type 'x86' conflicts with target machine type 'x64'  
    C:\vcpkg\installed\x86-windows\lib\libpng16.lib : warning LNK4272: library machine type 'x86' conflicts with target machine type 'x64'  
    C:\vcpkg\installed\x86-windows\lib\tiff.lib : warning LNK4272: library machine type 'x86' conflicts with target machine type 'x64'  
    bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\address-model-64\asynch-exceptions-on\threading-multi\all_formats_test.exe : fatal error LNK1120: 107 unresolved externals  
  
            call "bin.v2\standalone\msvc\msvc-14.2\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
    link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console   
        /out:"bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\address-model-64\asynch-exceptions-on\threading-multi\all_formats_test.exe"  
        /LIBPATH:"C:\vcpkg\installed\x86-windows\lib"    
        @"bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\address-model-64\asynch-exceptions-on\threading-multi\all_formats_test.exe.rsp"  
    ```  
  
### Problem 2:  `address-model=64,32` linking 32-bit target against 64-bit libraries  
  
    ```console  
    cd D:\boost.win  
    rmdir /s /q bin.v2  
    b2.exe toolset=msvc-14.2 address-model=64,32 libs/gil/test/extension/io//simple  
    Performing configuration checks  
  
        - default address-model    : 32-bit  
        - default architecture     : x86  
        - symlinks supported       : no  
        - junctions supported      : yes  
        - hardlinks supported      : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - BOOST_COMP_GNUC >= 4.3.0 : no  
        - libjpeg                  : yes  
        - zlib                     : yes  
        - libpng                   : yes  
        - libtiff                  : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - Boost.Config Feature Check: cxx11_constexpr : yes  
        - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
        - Boost.Config Feature Check: cxx11_template_aliases : yes  
        - Boost.Config Feature Check: cxx11_trailing_result_types : yes  
        - Boost.Config Feature Check: cxx11_variadic_templates : yes  
        - BOOST_COMP_GNUC >= 4.3.0 : no  
        - libjpeg                  : yes  
        - zlib                     : yes  
        - libpng                   : yes  
        - libtiff                  : yes  
    ...  
    C:\vcpkg\installed\x64-windows\lib\jpeg.lib : warning LNK4272: library machine type 'x64' conflicts with target machine type 'x86'  
    C:\vcpkg\installed\x64-windows\lib\zlib.lib : warning LNK4272: library machine type 'x64' conflicts with target machine type 'x86'  
    C:\vcpkg\installed\x64-windows\lib\libpng16.lib : warning LNK4272: library machine type 'x64' conflicts with target machine type 'x86'  
    C:\vcpkg\installed\x64-windows\lib\tiff.lib : warning LNK4272: library machine type 'x64' conflicts with target machine type 'x86'  
    bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\asynch-exceptions-on\threading-multi\all_formats_test.exe : fatal error LNK1120: 107 unresolved externals  
  
            call "bin.v2\standalone\msvc\msvc-14.2\msvc-setup.bat"  >nul  
    link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X86 /MANIFEST /subsystem:console   
         /out:"bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\asynch-exceptions-on\threading-multi\all_formats_test.exe"  
         /LIBPATH:"C:\vcpkg\installed\x64-windows\lib"    
         @"bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\asynch-exceptions-on\threading-multi\all_formats_test.exe.rsp"  
    ```  
  
### Boost.GIL Jamfile-s  
  
A word on the Jamfiles that are used above, may be relevant:  
  
GIL's [test/extension/io/Jamfile](https://github.com/boostorg/gil/blob/9e76b8b5bf7a16c50b7940c969e473855098f337/test/extension/io/Jamfile#L13-L16) has these `using`-s which, as I have been told, should not really be in `Jamfile`:  
  
```  
using libjpeg : : : : true ; # work around bug on master  
using zlib ;  
using libpng : : : : true ;  
using libtiff : : : : true ;  
```  
  
I removed them and tried the commands above. No difference, so I assume presence of those is benign.

---

## Comment 1

> Username: sjcooke  
> Created at: 2020-07-24 16:51:06 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663632675  

The problem appears to be due to having multiple [`searched-lib-target`](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/generators/searched-lib-generator.jam#L58) objects that differ only in their search paths. These objects are created correctly [here](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/generators/searched-lib-generator.jam#L44-L45) but then they pass through a [call](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/generators/searched-lib-generator.jam#L51) to [`virtual-target.register`](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/build/virtual-target.jam#L1001-L1046). The registration is supposed to ensure that identical objects have only a single instance.  
  
The problem with the registration of multiple `searched-lib-target` instances for the same library is that their `search` values are not included in the comparison and they are otherwise identical, so the first instance created is returned each time.  
  
A minimal test case is a Jamfile:  
```  
lib Lib : : <name>Lib <search>LIB32 <address-model>32 ;  
lib Lib : : <name>Lib <search>LIB64 <address-model>64 ;  
exe main : main.cpp Lib ;  
```  
where `main.cpp` can be an empty file.  
  
Running `b2.exe -n address-model=32,64` generates the following link commands:  
```  
link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X64 /MANIFEST:EMBED /subsystem:console   
    /out:"bin\msvc-14.1\debug\address-model-64\threading-multi\main.exe" /LIBPATH:"LIB32"     
    @"bin\msvc-14.1\debug\address-model-64\threading-multi\main.exe.rsp"  
```  
```  
link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X86 /MANIFEST:EMBED /subsystem:console   
    /out:"bin\msvc-14.1\debug\address-model-32\threading-multi\main.exe" /LIBPATH:"LIB32"     
    @"bin\msvc-14.1\debug\address-model-32\threading-multi\main.exe.rsp"  
```  
with the first command (`address-model=64`)  containing the incorrect `/LIBPATH:"LIB32"`.  
  
The problem is not unique to `address-model` and similar behavior can be observed with `release` and `debug` variants, for example.  
  
A simple fix would be to bypass the registration of `searched-lib-target` objects and return the object directly, replacing the expression `[ virtual-target.register $(t) ]` with simply `$(t)`. These objects have no action, and identical copies might be acceptable. Alternatively, a true comparison including the `search` value may be safer.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-07-24 17:04:05 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663638123  

But the objects don't just differ in their `<search>`. They differ in the relevant property `<address-model>`.

---

## Comment 3

> Username: sjcooke  
> Created at: 2020-07-24 18:25:25 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663672717  

The comparison code [here](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/build/virtual-target.jam#L1022-L1031) is not detecting the relevant property.  
  
For the test case above the variables are assigned values as follows:  
```  
ps1 [ <address-model>32 <link>shared <name>Lib <relevant>address-model <relevant>link <search>LIB32 ... ]  
ps2 [ <address-model>64 <link>shared <name>Lib <relevant>address-model <relevant>link <search>LIB64 ... ]  
relevant [ ]  
relevant [ <relevant>link ]  
p1 [ <link>shared ]  
p2 [ <link>shared ]  
```  
and therefore the (incorrect) cached object is returned.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-07-24 19:12:41 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663691086  

Odd. `<address-model>` should be considered relevant for the `msvc` toolset, but it appears that it isn't; I'm not sure why. `toolset.relevant` seems to look at the toolset flags and deduce the relevant features from there, but I have no idea what goes wrong there, or why it returns an empty list. What's `[ $(a1).action-name ]`, the `rule-name` parameter to `toolset.relevant`?

---

## Comment 5

> Username: sjcooke  
> Created at: 2020-07-24 19:48:09 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663704580  

The action name is `%.no-action`. I get exactly the same results with the `clang-win` toolset, so I'm not sure it is a toolset issue.  
  
Should the `searched-lib-target` method call `[ $(target).relevant ]` report also the `<relevant>...` properties of its action?

---

## Comment 6

> Username: pdimov  
> Created at: 2020-07-24 23:14:26 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663769302  

Seems like `<address-model>` (and, I suppose, `<architecture>`, and maybe others) need to be marked relevant either here:  
  
https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/generators/searched-lib-generator.jam#L41  
  
or here:  
  
https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/generators/searched-lib-generator.jam#L50  
  
or perhaps in both places (where currently only `<link>` is considered relevant.)  
  
Would be nice however if we hear from someone who actually knows what's going on here. @grafikrobot? @vprus? @swatanabe?

---

## Comment 7

> Username: pdimov  
> Created at: 2020-07-24 23:17:47 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663770023  

Or is the right place here?  
  
https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/generators/searched-lib-generator.jam#L91

---

## Comment 8

> Username: sjcooke  
> Created at: 2020-07-25 01:30:26 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663790646  

The same problem occurs for a similar case with `<variant>release` & `<variant>debug`, for example:  
```  
ps1 [ <link>shared <name>Lib <relevant>link <relevant>variant <search>Release <variant>release ... ]  
ps2 [ <link>shared <name>Lib <relevant>link <relevant>variant <search>Debug   <variant>debug   ... ]  
relevant [ ]  
relevant [ <relevant>link ]  
p1 [ <link>shared ]  
p2 [ <link>shared ]  
```  
 so I think that the `<relevant>...` values from `ps1` and/or `ps2` above should be part of the comparison, perhaps via the `searched-lib-target.relevant` method.

---

## Comment 9

> Username: mloskot  
> Created at: 2020-07-25 11:51:35 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663846596  

@sjcooke @pdimov Thanks for picking up this issue. I'm sorry, but I can't offer anything helpful.

---

## Comment 10

> Username: pdimov  
> Created at: 2020-07-25 15:52:39 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-663870428  

This was last touched by https://github.com/boostorg/build/commit/0b2643eb41f0c4050eb6c85a96a7257e5c0f4888 and could be a regression.

---

## Comment 11

> Username: swatanabe  
> Created at: 2020-07-27 01:01:08 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-664067248  

AMDG  
  
On 7/24/20 7:18 PM, Peter Dimov wrote:  
> Or is the right place here?  
>   
> https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/generators/searched-lib-generator.jam#L91  
>   
  
search and name should be added in all three locations.  This line  
is the one that will fix the bug.  <address-model>, <architecture>,  
and others do not directly affect searched-lib targets, so they  
should not be added here.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: swatanabe  
> Created at: 2020-07-27 01:16:04 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-664070209  

AMDG  
  
Actually, <name> is already handled for searched-lib-target in the  
target-name.  <name> should still be added in the usage  
requirements from searched-lib-generator.run, however.  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: sjcooke  
> Created at: 2020-07-27 19:04:42 UTC  
> Url: https://github.com/boostorg/build/issues/515#issuecomment-664581721  

I can confirm that this solves the issue for the test cases, with `p1` and `p2` now including the appropriate `<search>` paths.
