# #111 - "Ambiguous key" error when building on Windows, not Linux or OS X. [Closed]

> Username: tee3  
> Created at: 2015-12-03 23:01:31 UTC  
> Updated at: 2015-12-04 03:39:50 UTC  
> Closed at: 2015-12-04 03:39:50 UTC  
> Url: https://github.com/boostorg/build/issues/111  

I have run into an error when using a custom toolset under Windows.  I have developed and been using a [toolset](https://github.com/tee3/boost-build-ti) for the TI Code Generation Tools (cross-compiler for TI DSP) under Linux, OS X, and Cygwin.  I have been developing a [toolset tester](https://github.com/tee3/boost-build-toolset-tester) to ensure that everything for these toolsets work.  Incidentally, is there a better way to test a toolset within the Boost.Build project?  
  
I am now trying to use it under Windows and running in the error below.  I've worked around the issue by changing `property.jam` (see the last commit of https://github.com/tee3/build/commits/devel-workaround-for-possible-windows-bug), but I'm looking for a real fix wherever it needs to be.  
  
I'm hoping that someone sees the description and can check this without needing to run this toolset.  If not, and you feel like spending a lot of time on this problem, I've included instructions for reproducing the problem using a free TI compiler.  
  
```  
b2 --test-config=user-config.jam link=static -a  
building for the following toolsets:  
   * tms320c6000-8.0.4  
C:/Users/xxx/Development/boost-build/src/build\property.jam:743: in class@property-map.find from module object(property-map)@26  
error: Ambiguous key   
C:/Users/xxx/Development/boost-build/src/build\type.jam:333: in generated-target-ps from module type  
C:/Users/xxx/Development/boost-build/src/build\type.jam:270: in type.generated-target-suffix from module type  
C:/Users/xxx/Development/boost-build/src/build\virtual-target.jam:509: in virtual-target.add-prefix-and-suffix from module virtual-target  
C:/Users/xxx/Development/boost-build/src/build\virtual-target.jam:468: in _adjust-name from module object(file-target)@215  
C:/Users/xxx/Development/boost-build/src/build\virtual-target.jam:253: in abstract-file-target.__init__ from module object(file-target)@215  
C:/Users/xxx/Development/boost-build/src/build\virtual-target.jam:561: in class@file-target.__init__ from module object(file-target)@215  
C:/Users/xxx/Development/boost-build/src/kernel\class.jam:90: in class.new from module class  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:558: in generated-targets from module object(C-compiling-generator)@115  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:453: in construct-result from module object(C-compiling-generator)@115  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:412: in run-really from module object(C-compiling-generator)@115  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:386: in class@generator.run from module object(C-compiling-generator)@115  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:994: in try-one-generator-really from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:1056: in try-one-generator from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:1294: in construct-really from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:1378: in construct from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:1069: in generators.construct-types from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:609: in convert-to-consumable-types from module object(generator)@138  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:405: in run-really from module object(generator)@138  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:386: in class@generator.run from module object(generator)@138  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:994: in try-one-generator-really from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:1056: in try-one-generator from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:1294: in construct-really from module generators  
C:/Users/xxx/Development/boost-build/src/build\generators.jam:1378: in generators.construct from module generators  
C:/Users/xxx/Development/boost-build/src/build\targets.jam:1553: in construct from module object(typed-target)@152  
C:/Users/xxx/Development/boost-build/src/build\targets.jam:1353: in class@basic-target.generate from module object(typed-target)@152  
C:/Users/xxx/Development/boost-build/src/build\targets.jam:774: in generate-really from module object(main-target)@192  
C:/Users/xxx/Development/boost-build/src/build\targets.jam:746: in class@main-target.generate from module object(main-target)@192  
C:/Users/xxx/Development/boost-build/src/build\targets.jam:272: in class@project-target.generate from module object(project-target)@119  
C:/Users/xxx/Development/boost-build/src\build-system.jam:710: in load from module build-system  
C:\Users\xxx\Development\boost-build\src\kernel\modules.jam:295: in import from module modules  
C:\Users\xxx\Development\boost-build\src\kernel\bootstrap.jam:139: in boost-build from module  
C:\Users\xxx\Development\boost-build\boost-build.jam:8: in module scope from module  
```  
  
Follow the very long list of steps below to reproduce the problem.  
1.  Install at least one of the compilers and disabled the rest of the compilers in `project-config.jam` are commented out.  I would recommend installing [TI CGT TMS320C6000 8.0.4 for WIndows](http://software-dl.ti.com/codegen/esd/cgt_public_sw/C6000/8.0.4/ti_cgt_c6000_8.0.4_windows_installer.exe) as it does not have export restrictions as the other versions do.  The compilers are available for Linux as OS X as well, see [here](http://software-dl.ti.com/codegen/non-esd/downloads/download.htm).  Downloading the compilers requires registering for a free TI login account, so I understand if no one wants to investigate this.  
     
   I recommend installing to `%HOME%\opt\ti\ti-cgt-c6000-8.0.4`, but anywhere you have access will do and can be set up in `project-config.jam` as described below.  
2. Run the following commands to see the failure.   At the point where the error occurs in `property.jam`, `$(best)` is `obj obj` instead of just `obj`, resulting in an ambiguous key.  
     
   ``` bat  
   C:\> mkdir C:\TEMP  
   C:\> cd C:\TEMP  
   C:\TEMP\> git clone https://github.com/tee3/build build  
   C:\TEMP\> cd build  
   C:\TEMP\> git checkout develop  
   C:\TEMP\boost-build\> bootstrap.bat  
   C:\TEMP\boost-build\> cd ..  
   C:\TEMP\> git clone https://github.com/tee3/boost-build-ti  
   C:\TEMP\> git clone https://github.com/tee3/boost-build-toolset-tester  
   C:\TEMP\> cd boost-build-toolset-tester  
   C:\TEMP\> rem modify project-config.jam to reflect your configuration  
   C:\TEMP\> b2 --test-config=user-config.jam link=static -a  
   ```  
3. The last commit on https://github.com/tee3/build/commits/devel-workaround-for-possible-windows-bug works around the issue.  This does not address the logic, but just works around the immediate problem.    
     
   After this, the build then runs.  It fails for other reasons (I'm still working out getting the tools to work properly as they make a lot of assumptions, like the assumption that the current user has exclusive access to the tools when it builds the standard library on demand).  I believe the rest of the issues are surmountable within the toolset definition and the user's environment, but the issue above is not.

---

## Comment 1

> Username: swatanabe  
> Created at: 2015-12-04 00:45:13 UTC  
> Url: https://github.com/boostorg/build/issues/111#issuecomment-161838787  

AMDG  
  
On 12/03/2015 04:01 PM, tee3 wrote:  
  
> I am now trying to use it under Windows and running in the error below.  I've worked around the issue by changing `property.jam` (see the last commit of https://github.com/tee3/build/commits/devel-workaround-for-possible-windows-bug), but I'm looking for a real fix wherever it needs to be.  
  
  This isn't exactly a bug.  The problem is  
the following definitions in tools/types/exe.jam.  
(There are probably similar definitions for obj  
and lib)  
  
type.set-generated-target-suffix EXE : <target-os>windows : "exe" ;  
type.set-generated-target-suffix EXE : <target-os>cygwin : "exe" ;  
  
  On Windows, this also matches, and Boost.Build has no  
way to know that your definition should take priority.  
The easiest workaround is to add a definition that specifies  
both your toolset and <target-os>windows to disambiguate.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: tee3  
> Created at: 2015-12-04 01:19:15 UTC  
> Url: https://github.com/boostorg/build/issues/111#issuecomment-161844141  

@swatanabe Thanks so much, that is a better workaround than changing Boost.Build.  
  
However, the truth is that the `target-os` for this toolset is really `bare-metal` (or maybe `coff` or `elf` or something like that) and definitely not ever `windows`.  I see now that I'm building for `target-os = host-os` since that is the default behavior.  Do you think a better longer term answer for this toolset is to only allow it to work for a compatible `target-os`?  
  
For others, the change to the toolset was something like the following:  
  
``` diff  
diff --git a/tms320c2000.jam b/tms320c2000.jam  
index 68be0de..3fb7352 100644  
--- a/tms320c2000.jam  
+++ b/tms320c2000.jam  
@@ -88,11 +88,8 @@ rule init ( version ? : command * : options * )  
 # type.register A28 : a28 l28 ;  
  
 type.set-generated-target-suffix EXE : <toolset>tms320c2000 : out ;  
-type.set-generated-target-suffix EXE : <toolset>tms320c2000 <target-os>windows : out ;  
 type.set-generated-target-suffix OBJ : <toolset>tms320c2000 : obj ;  
-type.set-generated-target-suffix OBJ : <toolset>tms320c2000 <target-os>windows : obj ;  
 type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c2000 : lib ;  
-type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c2000 <target-os>windows : lib ;  
  
 generators.register-linker tms320c2000.link : OBJ STATIC_LIB : EXE : <toolset>tms320c2000 ;  
 # generators.register-linker tms320c2000.link : OBJ O28 A28 STATIC_LIB : EXE : <toolset>tms320c2000 <architecture>tms320c2000 <instruction-set>tms320c2800 ;  
diff --git a/tms320c5400.jam b/tms320c5400.jam  
index 8aa079b..82a4b4f 100644  
--- a/tms320c5400.jam  
+++ b/tms320c5400.jam  
@@ -88,11 +88,8 @@ type.register O54 : o54 ;  
 type.register A54 : a54 l54 ;  
  
 type.set-generated-target-suffix EXE : <toolset>tms320c5400 : out ;  
-type.set-generated-target-suffix EXE : <toolset>tms320c5400 <target-os>windows : out ;  
 type.set-generated-target-suffix OBJ : <toolset>tms320c5400 : obj ;  
-type.set-generated-target-suffix OBJ : <toolset>tms320c5400 <target-os>windows : obj ;  
 type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c5400 : lib ;  
-type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c5400 <target-os>windows : lib ;  
  
 generators.register-linker tms320c5400.link : OBJ STATIC_LIB : EXE : <toolset>tms320c5400 ;  
 #generators.register-linker tms320c5400.link : OBJ O54 A54 STATIC_LIB : EXE : <toolset>tms320c5400 ;  
diff --git a/tms320c5500.jam b/tms320c5500.jam  
index 588d0d9..d3a0b84 100644  
--- a/tms320c5500.jam  
+++ b/tms320c5500.jam  
@@ -88,11 +88,8 @@ type.register O55 : o55 ;  
 type.register A55 : a55 l55 ;  
  
 type.set-generated-target-suffix EXE : <toolset>tms320c5500 : out ;  
-type.set-generated-target-suffix EXE : <toolset>tms320c5500 <target-os>windows : out ;  
 type.set-generated-target-suffix OBJ : <toolset>tms320c5500 : obj ;  
-type.set-generated-target-suffix OBJ : <toolset>tms320c5500 <target-os>windows : obj ;  
 type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c5500 : lib ;  
-type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c5500 <target-os>windows : lib ;  
  
 generators.register-linker tms320c5500.link : OBJ STATIC_LIB : EXE : <toolset>tms320c5500 ;  
 #generators.register-linker tms320c5500.link : OBJ O55 A55 STATIC_LIB : EXE : <toolset>tms320c5500 <architecture>tms320c5500 <instruction-set>tms320c5500 ;  
diff --git a/tms320c6000.jam b/tms320c6000.jam  
index 67d2a80..75a5eea 100644  
--- a/tms320c6000.jam  
+++ b/tms320c6000.jam  
@@ -100,11 +100,8 @@ type.register A67 : a67 l67 ;  
 type.register A67P : a67p a67P l67p l67P ;  
  
 type.set-generated-target-suffix EXE : <toolset>tms320c6000 : out ;  
-type.set-generated-target-suffix EXE : <toolset>tms320c6000 <target-os>windows : out ;  
 type.set-generated-target-suffix OBJ : <toolset>tms320c6000 : obj ;  
-type.set-generated-target-suffix OBJ : <toolset>tms320c6000 <target-os>windows : obj ;  
 type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c6000 : lib ;  
-type.set-generated-target-suffix STATIC_LIB : <toolset>tms320c6000 <target-os>windows : lib ;  
  
 generators.register-linker tms320c6000.link : OBJ O62 A62 STATIC_LIB : EXE : <toolset>tms320c6000 ;  
 generators.register-linker tms320c6000.link : OBJ O62 O64 O64P A62 A64 STATIC_LIB : EXE : <toolset>tms320c6000 <architecture>tms320c6000 <instruction-set>tms320c6400 ;  
```

---

## Comment 3

> Username: tee3  
> Created at: 2015-12-04 03:39:50 UTC  
> Url: https://github.com/boostorg/build/issues/111#issuecomment-161865642  

I'm closing this as my immediate issue is resolved.
