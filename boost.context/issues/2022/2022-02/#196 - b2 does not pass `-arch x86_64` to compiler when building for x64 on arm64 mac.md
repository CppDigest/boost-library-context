# #196 - b2 does not pass `-arch x86_64` to compiler when building for x64 on arm64 mac [Closed]

> Username: autoantwort  
> Created at: 2022-02-08 17:43:02 UTC  
> Updated at: 2022-02-08 17:46:09 UTC  
> Closed at: 2022-02-08 17:45:38 UTC  
> Url: https://github.com/boostorg/context/issues/196  

It passes the flags to the compiler when building c++ files, but not when building assembly files. See https://github.com/microsoft/vcpkg/issues/22999  
  
For c++ file:  
```  
"/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++" -x c++ -arch x86_64 -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.3.sdk -fPIC -g -std=c++11 -stdlib=libc++ -m64 -O0 -fno-inline -Wall -g -D_DARWIN_C_SOURCE -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_SOURCE -I"../include" -I"/Users/samuelmorris/Development/vcpkg/installed/x64-osx/include" -c -o "/Users/samuelmorris/Development/vcpkg/buildtrees/boost-context/x64-osx-dbg/boost/build/871eed1ad314e3fe7b41c3cf2fde70f0/posix/stack_traits.o" "../src/posix/stack_traits.cpp"  
```  
For assembly file:  
```  
"/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++" -x assembler-with-cpp -m64 -O0 -fno-inline -Wall -g -D_DARWIN_C_SOURCE  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_SOURCE -I"../include" -I"/Users/samuelmorris/Development/vcpkg/installed/x64-osx/include" -c -o "/Users/samuelmorris/Development/vcpkg/buildtrees/boost-context/x64-osx-dbg/boost/build/871eed1ad314e3fe7b41c3cf2fde70f0/asm/ontop_x86_64_sysv_macho_gas.o" "../src/asm/ontop_x86_64_sysv_macho_gas.S"  
  
../src/asm/ontop_x86_64_sysv_macho_gas.S:32:10: error: unknown token in expression  
    movq %rdx, %r8  
```

---

## Comment 1

> Username: olk  
> Created at: 2022-02-08 17:45:38 UTC  
> Updated at: 2022-02-08 17:46:09 UTC  
> Url: https://github.com/boostorg/context/issues/196#issuecomment-1032889951  

This is a problem of boost.build / b2 - please report it to boost.build
