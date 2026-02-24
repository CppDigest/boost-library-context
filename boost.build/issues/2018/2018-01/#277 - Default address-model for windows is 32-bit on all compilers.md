# #277 - Default address-model for windows is 32-bit on all compilers? [Closed]

> Username: jeking3  
> Created at: 2018-01-07 15:51:33 UTC  
> Updated at: 2020-09-11 19:14:12 UTC  
> Closed at: 2020-09-11 19:14:12 UTC  
> Url: https://github.com/boostorg/build/issues/277  

When I build Boost.DateTime using standard build settings the result is 32-bits:  
```  
c:\boost\libs\date_time\test>..\..\..\b2 toolset=msvc-14.1 testtime_serialize_versioning_prev testtime_serialize_versioning_curr  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...patience...  
...found 2917 targets...  
...updating 10 targets...  
compile-c-c++ ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_prev.test\msvc-14.1\debug\threadapi-win32\threading-multi\posix_time\testtime_serialize_versioning.obj  
testtime_serialize_versioning.cpp  
msvc.link ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_prev.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_prev.exe  
   Creating library ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_prev.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_prev.lib and object ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_prev.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_prev.exp  
msvc.manifest ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_prev.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_prev.exe  
testing.capture-output ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_prev.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_prev.run  
        1 file(s) copied.  
**passed** ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_prev.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_prev.test  
compile-c-c++ ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_curr.test\msvc-14.1\debug\threadapi-win32\threading-multi\posix_time\testtime_serialize_versioning.obj  
testtime_serialize_versioning.cpp  
msvc.link ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_curr.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_curr.exe  
   Creating library ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_curr.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_curr.lib and object ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_curr.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_curr.exp  
msvc.manifest ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_curr.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_curr.exe  
testing.capture-output ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_curr.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_curr.run  
        1 file(s) copied.  
**passed** ..\..\..\bin.v2\libs\date_time\test\testtime_serialize_versioning_curr.test\msvc-14.1\debug\threadapi-win32\threading-multi\testtime_serialize_versioning_curr.test  
...updated 10 targets...  
  
c:\boost\libs\date_time\test>dir *.v*  
 Volume in drive C is Local Disk  
 Volume Serial Number is F634-C3E0  
  
 Directory of c:\boost\libs\date_time\test  
  
01/07/2018  10:46 AM            11,983 Jamfile.v2  
01/07/2018  10:47 AM                62 time_duration_serialization.x32.v0  
01/07/2018  10:47 AM                74 time_duration_serialization.x32.v1  
               3 File(s)         12,119 bytes  
               0 Dir(s)  115,327,811,584 bytes free  
  
c:\boost\libs\date_time\test>  
```  
  
If sizeof(size_t) is 4, the file with x32 in the name is created, otherwise it would be x64.  
  
Shouldn't the default build model if otherwise unspecified be address-model=64 ?  
  
I tried this on msvc-10.0 and on msvc-14.1.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-09 15:30:56 UTC  
> Url: https://github.com/boostorg/build/issues/277#issuecomment-356317833  

Ideally, I think the default should match the default settings for a newly created IDE project, assuming no customization of any kind.  Note that this is less of an issue now, as the default setting of stage and install in Jamroot is to build both on windows.

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-03-12 17:44:42 UTC  
> Url: https://github.com/boostorg/build/issues/277#issuecomment-598330702  

Please, may we have the defaults match the host system? It is not logical to build x86 by default, especially when you may be on ARM platform.

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-03-12 23:55:22 UTC  
> Url: https://github.com/boostorg/build/issues/277#issuecomment-598482421  

This one can be closed as duplicate of #128
