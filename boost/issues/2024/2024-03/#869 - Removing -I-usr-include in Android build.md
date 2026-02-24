# #869 - Removing -I/usr/include in Android build [Open]

> Username: hipersayanX  
> Created at: 2024-03-13 14:27:35 UTC  
> Updated at: 2024-03-13 14:27:35 UTC  
> Url: https://github.com/boostorg/boost/issues/869  

Hi, I'm trying to compile Boost for Android, this is the code that I'm using.  
  
```  
./bootstrap.sh -with-toolset=gcc  
  
userConfigs=$srcdir/$_srcname/user-config.jam  
  
cat << EOF > "${userConfigs}"  
using clang : android : ${ANDROID_CXX} :  
<archiver>${ANDROID_AR} \  
<assembler>${ANDROID_AS}  
;  
EOF  
  
./b2 install \  
    --prefix="$srcdir/fakeinstall" \  
    --user-config="${userConfigs}" \  
    --layout=system \  
    --with-atomic \  
    --with-chrono \  
    --with-container \  
    --with-date_time \  
    --with-exception \  
    --with-fiber \  
    --with-filesystem \  
    --with-graph \  
    --with-graph_parallel \  
    --with-iostreams \  
    --with-locale \  
    --with-log \  
    --with-math \  
    --with-mpi \  
    --with-program_options \  
    --with-random \  
    --with-regex \  
    --with-serialization \  
    --with-system \  
    --with-test \  
    --with-thread \  
    --with-timer \  
    --with-type_erasure \  
    --with-wave \  
    --with-stacktrace \  
    -sICONV_PATH="${ANDROID_PREFIX}" \  
    variant=release \  
    debug-symbols=off \  
    runtime-link=shared \  
    link=shared,static \  
    target-os=android \  
    toolset=clang-android \  
    architecture=arm \  
    threading=multi \  
    address-model=64 \  
    cflags="-I${ANDROIND_PREFIX_INCLUDE}" \  
    cxxflags="-I${ANDROIND_PREFIX_INCLUDE}" \  
    linkflags="-L${ANDROIND_PREFIX_LIB}"  
```  
  
The build fails and this command popup (over many other error messages).  
  
```  
"/opt/android-ndk/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android24-clang++"   -I/opt/android-libs/aarch64/include  -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_MPI_DYN_LINK=1 -DBOOST_MPI_SOURCE=1 -DNDEBUG   -I"." -I"/usr/include"  -c -o "bin.v2/libs/mpi/build/clang-linux-android/release/local-visibility-global/target-os-android/threading-multi/visibility-hidden/cartesian_communicator.o" "libs/mpi/src/cartesian_communicator.cpp"  
```  
  
With this error message:  
  
```  
In file included from libs/mpi/src/cartesian_communicator.cpp:9:  
In file included from /opt/android-ndk/toolchains/llvm/prebuilt/linux-x86_64/bin/../sysroot/usr/include/c++/v1/algorithm:1710:  
In file included from /opt/android-ndk/toolchains/llvm/prebuilt/linux-x86_64/bin/../sysroot/usr/include/c++/v1/type_traits:538:  
/opt/android-ndk/toolchains/llvm/prebuilt/linux-x86_64/bin/../sysroot/usr/include/c++/v1/cstdint:149:5: error: <cstdint> tried including <stdint.h> but didn't find libc++'s <stdint.h> header.           This usually means that your header search paths are not configured properly.           The header search paths should contain the C++ Standard Library headers before           any C Standard Library, and you are probably using compiler flags that make that           not be the case.  
#   error <cstdint> tried including <stdint.h> but didn't find libc++'s <stdint.h> header. \  
    ^  
```  
  
And have found that removing the ```-I"/usr/include"``` flag and executing that command directly it compiles fine.  
The problem is that I have not found where that flag was introduced.  
Did tried grepping for _-I.*/include_ and _/usr/include_ and other similar expressions but did not found any meaningful result.  
I'm trying to compile Boost 1.84.0.
