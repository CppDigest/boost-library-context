# #133 - Build failure under Apple clang 14 [Closed]

> Username: anarthal  
> Created at: 2024-02-28 16:01:04 UTC  
> Updated at: 2024-04-01 01:19:34 UTC  
> Closed at: 2024-04-01 01:19:34 UTC  
> Url: https://github.com/boostorg/parser/issues/133  

Build log is here (edited link): https://github.com/boostorg/mysql/actions/runs/8084450940/job/22089745435  
  
This looks closely related to https://github.com/llvm/llvm-project/issues/54765. This build is in GHA using `macos-latest` (no container here since OSX doesn't support it).  
  
Compiler details as reported by `b2`:  
* Apple clang version 14.0.0 (clang-1400.0.29.202)  
* Target: x86_64-apple-darwin21.6.0  
* InstalledDir: /Applications/Xcode_14.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
* cxxstd=20  
* variant=debug,release (happens in both)

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-30 18:47:20 UTC  
> Url: https://github.com/boostorg/parser/issues/133#issuecomment-2028440042  

Unfortunately, C++20 mode is not going to work for any version of Clang before (LLVM) Clang 15.  Their concepts implementation does not play nice with libstdc++'s lib, nor does their own lib have all of <ranges> implemented properly, even though it defines `__cpp_lib_ranges`.  
  
There is a workaround, though.  You can define `BOOST_PARSER_DISABLE_CONCEPTS` and that should work even in C++20 builds, since it will use the non-concept C++17 implementations.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-03-30 18:54:03 UTC  
> Updated at: 2024-03-30 18:56:07 UTC  
> Url: https://github.com/boostorg/parser/issues/133#issuecomment-2028441434  

Ah.  I just realized the proper fix for this is for me to disable the use of concept constraints in Clang < 16.  I mentioned this in #131.
