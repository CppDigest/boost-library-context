# #300 - Boost 1.67.0: iostreams can't detect ZLIB library on Android [Closed]

> Username: ruslo  
> Created at: 2018-04-16 09:06:47 UTC  
> Updated at: 2018-05-14 19:32:56 UTC  
> Closed at: 2018-05-14 19:32:55 UTC  
> Url: https://github.com/boostorg/build/issues/300  

Boost.iostreams can't detect ZLIB library. The reason of the failure is library `rt` added to test. Quick search for `rt` library in Boost.iostreams sources don't give any results so I think it's a Boost.build problem.  
  
Steps to reproduce:  
```  
[boost_1_67_0]> ./bootstrap.sh  
```  
  
```  
[boost_1_67_0]> ./b2 \  
    -a \  
    link=static \  
    threading=multi \  
    variant=debug,release \  
    --layout=tagged \  
    toolset=gcc-ndk \  
    --user-config=/.../boost_1_67_0/boost.user.jam \  
    --with-iostreams \  
    -s \  
    NO_COMPRESSION=0 \  
    -s \  
    NO_ZLIB=0 \  
    -s \  
    NO_BZIP2=0 \  
    -s \  
    ZLIB_INCLUDE=/.../Install/include \  
    -s \  
    ZLIB_LIBPATH=/.../Install/lib \  
    -s \  
    ZLIB_BINARY=z \  
    -s \  
    BZIP2_INCLUDE=/.../Install/include \  
    -s \  
    BZIP2_LIBPATH=/.../Install/lib \  
    -s \  
    BZIP2_BINARY=bz2 \  
    linkflags="-Wl,--fix-cortex-a8 -fPIE -pie -Wl,--gc-sections -Wl,-z,nocopyreloc /.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/libs/armeabi-v7a/libgnustl_static.a -lstdc++ -lm -lgcc -lc -ldl -lgcc" \  
    address-model=32  
```  
  
Content of the `boost.user.jam` file:  
```  
using gcc  
 : ndk  
 : "/.../android-ndk-r10e/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-g++" -std=c++11 -march=armv7-a -marm -mfpu=neon -mfloat-abi=softfp -funwind-tables -no-canonical-prefixes -fexceptions -frtti --sysroot=/.../android-ndk-r10e/platforms/android-19/arch-arm -isystem /.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/include -isystem /.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/libs/armeabi-v7a/include -isystem /.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/include/backward -isystem /.../android-ndk-r10e/platforms/android-19/arch-arm/usr/include -fPIC -DANDROID  
 : <archiver>"/.../android-ndk-r10e/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-ar"  
 <ranlib>"/.../android-ndk-r10e/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-ranlib"  
;  
```  
  
According to logs ZLIB not found:  
```  
Building the Boost C++ Libraries.  
  
    - zlib                     : no  
    - bzip2                    : no  
    - lzma                     : no  
    - symlinks supported       : yes  
    - zlib                     : no  (cached)  
    - bzip2                    : no  (cached)  
    - lzma                     : no  (cached)  
```  
  
In `bin.v2/config.log` you can find the reason:  
  
```  
/.../android-ndk-r10e/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/../lib/gcc/arm-linux-androideabi/4.9/../../../../arm-linux-androideabi/bin/ld: error: cannot find -lrt  
collect2: error: ld returned 1 exit status  
```  
  
Library `rt` added to the test:  
  
```  
"/.../android-ndk-r10e/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-g++"  
    "-std=c++11"  
    "-march=armv7-a"  
    "-marm"  
    "-mfpu=neon"  
    "-mfloat-abi=softfp"  
    "-funwind-tables"  
    "-no-canonical-prefixes"  
    "-fexceptions"  
    "-frtti"  
    "--sysroot=/.../android-ndk-r10e/platforms/android-19/arch-arm"  
    "-isystem"  
    "/.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/include"  
    "-isystem"  
    "/.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/libs/armeabi-v7a/include"  
    "-isystem"  
    "/.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/include/backward"  
    "-isystem"  
    "/.../android-ndk-r10e/platforms/android-19/arch-arm/usr/include"  
    "-fPIC"  
    "-DANDROID"  
    -L"/.../Install/lib"  
    -Wl,-rpath  
    -Wl,"/.../Install/lib"  
    -Wl,-rpath-link  
    -Wl,"/.../Install/lib"  
    -o  
    "bin.v2/standalone/ac/gcc-ndk/debug/link-static/threading-multi/z"  
    -Wl,--start-group  
    "bin.v2/standalone/ac/gcc-ndk/debug/link-static/threading-multi/main-z.o"  
    -Wl,-Bstatic  
    -lz  
    -Wl,-Bdynamic  
    -lrt # <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<  
    -Wl,--end-group  
    -pthread  
    -g  
    -Wl,--fix-cortex-a8  
    -fPIE  
    -pie  
    -Wl,--gc-sections  
    -Wl,-z,nocopyreloc  
    /.../android-ndk-r10e/sources/cxx-stl/gnu-libstdc++/4.9/libs/armeabi-v7a/libgnustl_static.a  
    -lstdc++  
    -lm  
    -lgcc  
    -lc  
    -ldl  
    -lgcc  
```

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-05-13 02:31:14 UTC  
> Url: https://github.com/boostorg/build/issues/300#issuecomment-388596569  

AMDG  
  
On 04/16/2018 03:06 AM, Ruslan Baratov wrote:  
> Boost.iostreams can't detect ZLIB library. The reason of the failure is library `rt` added to test. Quick search for `rt` library in Boost.iostreams sources don't give any results so I think it's a Boost.build problem.  
>   
  
Try adding target-os=android.  (This isn't auto-detected yet.)  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: ruslo  
> Created at: 2018-05-14 19:32:55 UTC  
> Url: https://github.com/boostorg/build/issues/300#issuecomment-388935683  

> Try adding target-os=android  
  
It works now, thanks for the help.
