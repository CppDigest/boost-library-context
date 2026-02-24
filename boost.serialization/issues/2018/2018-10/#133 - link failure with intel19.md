# #133 - link failure with intel19 [Closed]

> Username: aminiussi  
> Created at: 2018-10-29 09:47:56 UTC  
> Updated at: 2018-10-31 07:33:48 UTC  
> Closed at: 2018-10-31 07:33:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/133  

Hi,   
  
On branch develop, on linux with inte2019 (icc version 19.0.0.117 (gcc version 4.8.5 compatibility)) my test build is failing with:  
  
```  
intel-linux.link.dll ../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/libpolymorphic_base.so.1.69.0  
intel-linux.archive ../../../bin.v2/libs/serialization/build/intel-linux/debug/link-static/visibility-hidden/libboost_serialization.a  
intel-linux.link.dll ../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/libmulti_shared_1.so.1.69.0  
ld: ../../../bin.v2/libs/serialization/build/intel-linux/debug/link-static/visibility-hidden/libboost_serialization.a(basic_oarchive.o): relocation R_X86_64_32 against `.rodata' can not be used when making a shared object; recompile with -fPIC  
../../../bin.v2/libs/serialization/build/intel-linux/debug/link-static/visibility-hidden/libboost_serialization.a: error adding symbols: Bad value  
  
    "icpc"   -o "../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/libmulti_shared_1.so.1.69.0" -Wl,-soname -Wl,libmulti_shared_1.so.1.69.0 -shared "../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/multi_shared1.o" "../../../bin.v2/libs/serialization/build/intel-linux/debug/link-static/visibility-hidden/libboost_serialization.a" "../../../bin.v2/libs/filesystem/build/intel-linux/debug/visibility-hidden/libboost_filesystem.so.1.69.0" "../../../bin.v2/libs/system/build/intel-linux/debug/visibility-hidden/libboost_system.so.1.69.0"     -fPIC -m64 -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed intel-linux.link.dll ../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/libmulti_shared_1.so.1.69.0...  
intel-linux.link.dll ../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/libmulti_shared_2.so.1.69.0  
ld: ../../../bin.v2/libs/serialization/build/intel-linux/debug/link-static/visibility-hidden/libboost_serialization.a(basic_oarchive.o): relocation R_X86_64_32 against `.rodata' can not be used when making a shared object; recompile with -fPIC  
../../../bin.v2/libs/serialization/build/intel-linux/debug/link-static/visibility-hidden/libboost_serialization.a: error adding symbols: Bad value  
  
    "icpc"   -o "../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/libmulti_shared_2.so.1.69.0" -Wl,-soname -Wl,libmulti_shared_2.so.1.69.0 -shared "../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/multi_shared2.o" "../../../bin.v2/libs/serialization/build/intel-linux/debug/link-static/visibility-hidden/libboost_serialization.a" "../../../bin.v2/libs/filesystem/build/intel-linux/debug/visibility-hidden/libboost_filesystem.so.1.69.0" "../../../bin.v2/libs/system/build/intel-linux/debug/visibility-hidden/libboost_system.so.1.69.0"     -fPIC -m64 -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed intel-linux.link.dll ../../../bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden/libmulti_shared_2.so.1.69.0...  
...failed updating 3 targets...  
...skipped 1 target...  
  
```  
  
Before that, I get the following test failure:  
  
```  
./../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
No errors detected.  
  
EXIT STATUS: 139  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/beegfs/SCRATCH/alainm/views/boost/bin.v2/libs/filesystem/build/intel-linux/debug/visibility-hidden:/beegfs/SCRATCH/alainm/views/boost/bin.v2/libs/serialization/build/intel-linux/debug/visibility-hidden:/beegfs/SCRATCH/alainm/views/boost/bin.v2/libs/serialization/test/intel-linux/debug/visibility-hidden:/beegfs/SCRATCH/alainm/views/boost/bin.v2/libs/system/build/intel-linux/debug/visibility-hidden:/trinity/shared/apps/170135/x86_64/intel-2019.0.045/compilers_and_libraries_2019.0.117/linux/bin/lib:/trinity/shared/apps/170135/x86_64/intel-2019.0.045/compilers_and_libraries_2019.0.117/linux/lib/intel64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported"   > "../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.output" "../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/serialization/test/test_dll_exported.test/intel-linux/debug/visibility-hidden/test_dll_exported.run...  
```  
  
Regards

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-30 15:01:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/133#issuecomment-434336405  

I've been working on this area.  Hopefully if you run the test again it should pass now (on develop)

---

## Comment 2

> Username: aminiussi  
> Created at: 2018-10-31 07:33:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/133#issuecomment-434588768  

Thanks!
