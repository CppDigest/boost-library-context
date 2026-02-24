# #739 - Building Boost and B2 using Intel OneAPI [Open]

> Username: BryanFlynt-NOAA  
> Created at: 2021-09-28 17:01:03 UTC  
> Updated at: 2024-09-21 07:15:36 UTC  
> Url: https://github.com/boostorg/build/issues/739  

I've encountered 2 problems using the Intel OneAPI compilers.  The first one I have the fix for but the second is more complex.  
  
## Problem 1 (Bootstrap):  
Running bootstrap.sh with the intel-linux tool set insists on linking the application statically.  Many OS do not include static libm.a, libdl.a and libc.a libraries but they do exist as shared libraries (*.so files).  The other tool sets (GCC, etc.) do not impose this static requirement.  
  
Linux Terminal Commands to Reproduce:  
```  
> cd boost_1_76_0  
> ./bootstrap.sh --with-toolset=intel-linux  
> icpx -x c++ -std=c++11 -O3 -s -static -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp execunix.cpp filesys.cpp filent.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp -o b2  
/usr/bin/ld: cannot find -lm  
/usr/bin/ld: cannot find -ldl  
/usr/bin/ld: cannot find -lc  
```  
  
  
#### The Fix:  
Remove the "-static" flag from the intel-linux compiler flags at line ~334 of "tools/build/src/engine/build.sh" and b2 will build properly.  
  
  
## Problem 2 (Building Boost):  
Running ./b2 appears to overwrite the system environment variables when building Boost to the incorrect locations.  
  
Linux Terminal Commands to Reproduce (after b2 correctly built as above):  
```  
> cd boost_1_76_0  
> ./b2  
icpx: error while loading shared libraries: libimf.so: cannot open shared object file: No such file or directory  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/tools/intel-linux.jam:185: in intel-linux.init  
*** argument error  
* rule numbers.less ( n1 n2 )  
* called with: ( 10 )  
* missing argument n2  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/util/numbers.jam:66:see definition of rule 'numbers.less' being called  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/build/toolset.jam:44: in toolset.using  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/build/project.jam:1104: in using  
project-config.jam:12: in modules.load  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/build-system.jam:255: in load-config  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/build-system.jam:486: in load-configuration-files  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/build-system.jam:607: in load  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/kernel/modules.jam:295: in import  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
/home/bflynt/opt/modman/build/boost/1.76.0/oneapi/2021.3.0/boost-build.jam:17: in module scope  
```  
  
The missing library (libimf.so) exists within the first directory pointed at by the LD_LIBRARY_PATH environment variable. So it appears boost is overwriting the LD_LIBRARY_PATH environment variable when running ./b2  
  
```  
> echo ${LD_LIBRARY_PATH}  
/home/bflynt/opt/modman/apps/oneapi/2021.3.0/compiler/2021.3.0/linux/compiler/lib/intel64_lin:/home/bflynt/opt/modman/apps/oneapi/2021.3.0/compiler/2021.3.0/linux/lib/x64:/home/bflynt/opt/modman/apps/oneapi/2021.3.0/compiler/2021.3.0/linux/lib/emu:/home/bflynt/opt/modman/apps/oneapi/2021.3.0/compiler/2021.3.0/linux/lib:/home/bflynt/opt/modman/apps/gcc/11.1.0/lib64:/home/bflynt/opt/modman/apps/gcc/11.1.0/lib  
>  
> ls /home/bflynt/opt/modman/apps/oneapi/2021.3.0/compiler/2021.3.0/linux/compiler/lib/intel64_lin  
cilk_db.so   libchkpwrap.a      libdecimal.a       libifcoremt.so.5  libifport.so.5  libiomp5.dbg      libirc.a       libistrconv.so  libqkmalloc.so  
crt          libchkpwrap_h.a    libicaf.so         libifcore_pic.a   libimf.a        libiomp5_db.so    libirc_s.a     libmatmul.a     libsvml.a  
for_main.o   libchkpwrap_h_w.a  libifcore.a        libifcore.so      libimf.so       libiomp5.so       libirc.so      libpdbx.a       libsvml.so  
init.o       libchkpwrap_w.a    libifcoremt.a      libifcore.so.5    libintlc.so     libiompstubs5.a   libirng.a      libpdbxinst.a   locale  
libbfp754.a  libcilkrts.so      libifcoremt_pic.a  libifport.a       libintlc.so.5   libiompstubs5.so  libirng.so     libpdbx.so  
libchkp.so   libcilkrts.so.5    libifcoremt.so     libifport.so      libiomp5.a      libipgo.a         libistrconv.a  libpdbx.so.5  
```

---

## Comment 1

> Username: MarDiehl  
> Created at: 2024-09-21 07:15:36 UTC  
> Url: https://github.com/boostorg/build/issues/739#issuecomment-2365039158  

wrong place, continuing at https://github.com/bfgroup/b2 (where my issue got no attention)
