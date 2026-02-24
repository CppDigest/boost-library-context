# #438 - [regression] Boost 1.70.0: iostreams can't detect ZLIB library on iOS [Open]

> Username: ruslo  
> Created at: 2019-05-13 09:12:56 UTC  
> Updated at: 2021-05-29 16:23:08 UTC  
> Url: https://github.com/boostorg/build/issues/438  

Xcode version:  
  
    > xcodebuild -version  
    Xcode 9.4.1  
    Build version 9F2000  
  
`boost.user.jam` content:  
  
    > export TOPDIR=`pwd`  
    > cat $TOPDIR/boost.user.jam  
  
    using darwin : iphoneos  
        : xcrun clang++  -arch armv7 -arch armv7s -arch arm64 -isysroot /Applications/develop/ide/xcode/9.4.1/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS11.4.sdk  
        : <striper> <root>/Applications/develop/ide/xcode/9.4.1/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer  
        : <architecture>arm <target-os>iphone  
    ;  
  
    using darwin : iphonesimulator  
        : xcrun clang++  -arch i386 -arch x86_64 -isysroot /Applications/develop/ide/xcode/9.4.1/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator11.4.sdk  
        : <striper> <root>/Applications/develop/ide/xcode/9.4.1/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer  
        : <architecture>x86 <target-os>iphone  
    ;  
  
Third party:  
  
    > ls $TOPDIR/third_party/include/{zlib.h,bzlib.h}  
  
    /.../third_party/include/bzlib.h  
    /.../third_party/include/zlib.h  
  
    > lipo -info $TOPDIR/third_party/lib/{libz.a,libbz2.a}  
  
    Architectures in the fat file: /.../third_party/lib/libz.a are: i386 armv7 armv7s x86_64 arm64   
    Architectures in the fat file: /.../third_party/lib/libbz2.a are: i386 armv7 armv7s x86_64 arm64  
  
Download and unpack:  
  
    > wget https://dl.bintray.com/boostorg/release/1.70.0/source/boost_1_70_0.tar.bz2  
    > tar xf boost_1_70_0.tar.bz2  
    > cd boost_1_70_0/  
  
Bootstrap and build:  
  
    > ./bootstrap.sh --with-libraries=iostreams --prefix=$TOPDIR/_install  
  
    > ./b2 \  
        "-d+2 --debug-configuration" \  
        toolset=darwin-iphoneos \  
        target-os=iphone \  
        architecture=arm \  
        instruction-set=armv7 \  
        -a \  
        link=static \  
        threading=multi \  
        variant=release,debug \  
        define=BOOST_SP_NO_SYNC \  
        --layout=tagged \  
        --user-config=$TOPDIR/boost.user.jam \  
        -s NO_COMPRESSION=0 \  
        -s NO_ZLIB=0 \  
        -s NO_BZIP2=0 \  
        -s ZLIB_INCLUDE=$TOPDIR/third_party/include \  
        -s ZLIB_LIBPATH=$TOPDIR/third_party/lib \  
        -s ZLIB_BINARY=z \  
        -s BZIP2_INCLUDE=$TOPDIR/third_party/include \  
        -s BZIP2_LIBPATH=$TOPDIR/third_party/lib \  
        -s BZIP2_BINARY=bz2 \  
        "cxxflags=-std=c++14 -miphoneos-version-min=9.3"  
  
Output:  
  
    Performing configuration checks  
        ...  
  
    Building the Boost C++ Libraries.  
  
        - zlib                     : no  
        - bzip2                    : no  
        ...  
  
    Component configuration:  
  
        ...  
        - iostreams                : building  
        ...  
  
In file `bin.v2/config.log`:  
  
    "xcrun" "clang++" "-arch" "armv7" "-arch" "armv7s" "-arch" "arm64"  
        "-isysroot" "/Applications/develop/ide/xcode/9.4.1/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS11.4.sdk"  
        -L"/.../third_party/lib"  
        -o "bin.v2/standalone/ac/darwin-iphoneos/release/architecture-arm/instruction-set-armv7/link-static/target-os-iphone/threading-multi/visibility-hidden/z"  
        "bin.v2/standalone/ac/darwin-iphoneos/release/architecture-arm/instruction-set-armv7/link-static/target-os-iphone/threading-multi/visibility-hidden/main-z.o"  
        -lrt  
        -lz  
        -pthread -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-dead_strip -no_dead_strip_inits_and_terms -arch armv7   
  
Error:  
  
    ld: library not found for -lrt  
  
The same configuration worked fine with Boost 1.65.1. Since Boost 1.66.0 it is not working.

---

## Comment 1

> Username: mateuszzz88  
> Created at: 2020-02-13 09:08:10 UTC  
> Url: https://github.com/boostorg/build/issues/438#issuecomment-585624166  

I also have this problem. Is there some workaround or fix?

---

## Comment 2

> Username: mateuszzz88  
> Created at: 2020-02-14 13:54:19 UTC  
> Url: https://github.com/boostorg/build/issues/438#issuecomment-586296782  

I worked it around by adding `-L.` in `tools/build/src/user-config.jam` and doing `ln -s $(xcrun --sdk iphoneos --show-sdk-path)/usr/lib/libpkstart.a librt.a` in sources dir. If script wants to link `-lrt` I will give it. I hoped that empty file would suffice, but it doesn't, so I linked random library that happens to really exist.   
This is embarrassing but seems to work.

---

## Comment 3

> Username: limbolily  
> Created at: 2020-12-01 11:16:10 UTC  
> Url: https://github.com/boostorg/build/issues/438#issuecomment-736442919  

I have the same problem. #678

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:39 UTC  
> Url: https://github.com/boostorg/build/issues/438#issuecomment-850859638  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
