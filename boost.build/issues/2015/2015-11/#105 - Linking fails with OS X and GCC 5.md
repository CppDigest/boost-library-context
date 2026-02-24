# #105 - Linking fails with OS X and GCC 5 [Closed]

> Username: thtrummer  
> Created at: 2015-11-14 16:03:09 UTC  
> Updated at: 2018-10-20 07:44:10 UTC  
> Closed at: 2018-10-20 07:44:10 UTC  
> Url: https://github.com/boostorg/build/issues/105  

When using an externally installed GCC version (e.g. from homebrew) linking fails due to unknown linker options.  
  
```  
gcc.link.dll bin.v2/libs/iostreams/build/gcc-5/release/threading-multi/libboost_iostreams.dylib  
ld: unknown option: -h  
collect2: error: ld returned 1 exit status  
  
    "g++-5"    -o "bin.v2/libs/iostreams/build/gcc-5/release/threading-multi/libboost_iostreams.dylib" -Wl,-h -Wl,libboost_iostreams.dylib -shared -Wl,--start-group "bin.v2/libs/iostreams/build/gcc-5/release/threading-multi/file_descriptor.o" "bin.v2/libs/iostreams/build/gcc-5/release/threading-multi/mapped_file.o" "bin.v2/libs/iostreams/build/gcc-5/release/threading-multi/bzip2.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lbz2 -Wl,--end-group -m64   
```  
  
This is due to the build system expecting the GNU linker however on OS X the default Apple linker is being used (which is correct, though, it doesn't understand the newer flags).

---

## Comment 1

> Username: vprus  
> Created at: 2015-11-19 18:05:05 UTC  
> Url: https://github.com/boostorg/build/issues/105#issuecomment-158139608  

Would it work to initialize gcc with:  
  
```  
using gcc : g++ : <linker-type>darwin ;  
```  
  
along with the below patch:  
  
```  
--- src/tools/gcc.jam  
+++ src/tools/gcc.jam  
@@ -813,6 +813,7 @@ rule init-link-flags ( toolset linker condition )  
         unchecked ;  
         toolset.flags $(toolset).link RPATH_LINK $(condition) : <xdll-path> :  
         unchecked ;  
+        toolset.flags $(toolset).link HAVE_SONAME : : unchecked ;  
  
     case gnu :  
         # Strip the binary when no debugging is needed. We use --strip-all flag  
```

---

## Comment 2

> Username: thtrummer  
> Created at: 2016-02-25 09:47:13 UTC  
> Url: https://github.com/boostorg/build/issues/105#issuecomment-188695441  

I've tried the patch but it doesn't seem to make a difference.   
- I've added the first line to my 'user-config.jam' and tried to build boost, however it fails:  
  
```  
Tom-Air:boost_1_60_0_B Tom$ ./b2 toolset=gcc  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/tools/gcc.jam:83: in gcc.init from module gcc  
error: toolset gcc initialization:  
error: provided command '<linker-type>darwin' not found  
error: initialized from /Users/Tom/user-config.jam:1  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/build/toolset.jam:43: in toolset.using from module toolset  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/build/project.jam:1052: in using from module project-rules  
/Users/Tom/user-config.jam:1: in modules.load from module user-config  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/build-system.jam:249: in load-config from module build-system  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/build-system.jam:389: in load-configuration-files from module build-system  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/build-system.jam:524: in load from module build-system  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/kernel/modules.jam:295: in import from module modules  
/Users/Tom/Downloads/boost_1_60_0_B/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/Users/Tom/Downloads/boost_1_60_0_B/boost-build.jam:17: in module scope from module  
```  
- I then added the version number of gcc and the build system seems to pick it up: `using gcc : 5.3.0 : g++-5 : <linker-type>darwin ;`  
- However even with the applied patch to gcc.jam the error messages are same. (I've applied it to a clean extract of the 1.60 release with a ./bootstrap.sh to build ./b2)
