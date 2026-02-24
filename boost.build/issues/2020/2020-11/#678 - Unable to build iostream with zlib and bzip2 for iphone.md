# #678 - Unable to build iostream with zlib and bzip2 for iphone [Open]

> Username: limbolily  
> Created at: 2020-11-30 17:34:59 UTC  
> Updated at: 2021-05-29 16:23:09 UTC  
> Url: https://github.com/boostorg/build/issues/678  

I use conan-center-index boost recipe for iphone boost building.The recipe finally output this command line:  
  
> b2 target-os=iphone architecture=arm address-model=64 binary-format=mach-o abi=aapcs --layout=system --user-config=/Users/limbo/.conan/data/boost/1.70.0/conan/stable/source/source_subfolder/tools/build/user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.iconv=on boost.locale.icu=off threading=multi link=static variant=release --without-python toolset=clang-darwin linkflags="-stdlib=libc++" pch=on cxxflags="-fPIC -stdlib=libc++ -mios-version-min=14.0 -DBOOST_AC_USE_PTHREADS -DBOOST_SP_USE_PTHREADS -fvisibility=hidden -fvisibility-inlines-hidden -fembed-bitcode" install --prefix=/Users/limbo/.conan/data/boost/1.70.0/conan/stable/package/529b6315d7b1db213c2e0211bde7d715f8837d72 -j4 --abbreviate-paths --debug-configuration --build-dir="/Users/limbo/.conan/data/boost/1.70.0/conan/stable/build/529b6315d7b1db213c2e0211bde7d715f8837d72  
  
It's failed to add bzip2.cpp and zlib.cpp to the iostream target because ac.check-library error.In the config.log file,it shows that linking a simple exe with zlib failed due to "--start-group" flag.  
  
  
> clang-darwin.compile.c++ /Users/limbo/.conan/data/boost/1.70.0/conan/stable/build/529b6315d7b1db213c2e0211bde7d715f8837d72/boost/bin.v2/standalone/ac/clng-drwn-12.0/rls/lnk-sttc/trgt-os-iphn/thrd-mlt/vsblt-hdn/main-z.o  
> clang-darwin.link /Users/limbo/.conan/data/boost/1.70.0/conan/stable/build/529b6315d7b1db213c2e0211bde7d715f8837d72/boost/bin.v2/standalone/ac/clng-drwn-12.0/rls/lnk-sttc/trgt-os-iphn/thrd-mlt/vsblt-hdn/z  
> ld: unknown option: --start-group  
> clang: error: linker command failed with exit code 1 (use -v to see invocation)  
>   
>     "/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang++" "-isysroot" "/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS14.2.sdk" "-arch" "arm64" -stdlib=libc++ -L"/Users/limbo/.conan/data/zlib/1.2.11/conan/stable/package/36b30d7320304477304df4f76faa382cbb7ae717/lib" -o "/Users/limbo/.conan/data/boost/1.70.0/conan/stable/build/529b6315d7b1db213c2e0211bde7d715f8837d72/boost/bin.v2/standalone/ac/clng-drwn-12.0/rls/lnk-sttc/trgt-os-iphn/thrd-mlt/vsblt-hdn/z" "/Users/limbo/.conan/data/boost/1.70.0/conan/stable/build/529b6315d7b1db213c2e0211bde7d715f8837d72/boost/bin.v2/standalone/ac/clng-drwn-12.0/rls/lnk-sttc/trgt-os-iphn/thrd-mlt/vsblt-hdn/main-z.o"  -Wl,--start-group -Wl,-Bstatic -lz -Wl,-Bdynamic -lrt -Wl,--end-group -pthread -fvisibility=hidden -fvisibility-inlines-hidden -isysroot/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS14.2.sdk -mios-version-min=14.0 -fembed-bitcode -arch arm64  
>   
> ...failed clang-darwin.link /Users/limbo/.conan/data/boost/1.70.0/conan/stable/build/529b6315d7b1db213c2e0211bde7d715f8837d72/boost/bin.v2/standalone/ac/clng-drwn-12.0/rls/lnk-sttc/trgt-os-iphn/thrd-mlt/vsblt-hdn/z...  
  
If I remove "Wl,--start-group", "-Wl,-Bstatic", "-Wl,-Bdynamic", "-lrt", "-Wl,--end-group" flags, the command above successfully output the correct executable.I'm not familiar with b2,but I'm curious about how these flags come out.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:40 UTC  
> Url: https://github.com/boostorg/build/issues/678#issuecomment-850859640  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
