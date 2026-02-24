# #716 - address-model=64 architecture=arm target-os=darwin generates x86_64 output [Open]

> Username: autoantwort  
> Created at: 2021-03-23 10:29:18 UTC  
> Updated at: 2021-05-29 16:22:50 UTC  
> Url: https://github.com/boostorg/build/issues/716  

This was reported by microsoft/vcpkg#16630 and I can reproduce it on my machine with `lipo -archs libboost_system.a` which returns `x86_64`

---

## Comment 1

> Username: autoantwort  
> Created at: 2021-03-23 11:01:24 UTC  
> Url: https://github.com/boostorg/build/issues/716#issuecomment-804811172  

Ok, it seems that the problem is the `--hash` parameter. Without `--hash` it will produce arm64 binaries, with `--hash` x86_64

---

## Comment 2

> Username: autoantwort  
> Created at: 2021-03-23 11:08:01 UTC  
> Url: https://github.com/boostorg/build/issues/716#issuecomment-804815282  

```  
➜  build git:(master) rm /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage/lib/libboost_system.a && cd /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/src/ost-1.75.0-2027312ef4.clean/build && /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build/b2 --stagedir=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage --build-dir=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg runtime-link=static link=static address-model=64 architecture=arm target-os=darwin toolset=clang threadapi=pthread  variant=debug --layout=system --with-atomic --with-random --with-date_time --with-filesystem --with-system --with-thread --with-chrono -j8 -sBOOST_ROOT=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build -sBOOST_BUILD_PATH=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build  threading=multi debug-symbols=on stage --hash  
...found 121 targets...  
...updating 1 target...  
common.copy /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage/lib/libboost_system.a  
...updated 1 target...  
➜  build git:(master) lipo -archs /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage/lib/libboost_system.a  
x86_64  
➜  build git:(master) rm /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage/lib/libboost_system.a && cd /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/src/ost-1.75.0-2027312ef4.clean/build && /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build/b2 --stagedir=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage --build-dir=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg runtime-link=static link=static address-model=64 architecture=arm target-os=darwin toolset=clang threadapi=pthread  variant=debug --layout=system --with-atomic --with-random --with-date_time --with-filesystem --with-system --with-thread --with-chrono -j8 -sBOOST_ROOT=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build -sBOOST_BUILD_PATH=/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build  threading=multi debug-symbols=on stage         
...found 126 targets...  
...updating 1 target...  
common.copy /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage/lib/libboost_system.a  
...updated 1 target...  
➜  build git:(master) lipo -archs /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-system/arm64-osx-dbg/stage/lib/libboost_system.a  
arm64  
```

---

## Comment 3

> Username: zhaoming029  
> Created at: 2021-04-21 08:28:05 UTC  
> Url: https://github.com/boostorg/build/issues/716#issuecomment-823881507  

@autoantwort   
Hi, is it possible that we can build universal libraries for boost? Here is my command:  
`./b2 --prefix="/Users/zhaoma/USD-21.02/output/3prelease" --build-dir="/Users/zhaoma/USD-21.02/release/build" -j12 address-model=64 link=shared runtime-link=shared threading=multi variant=release --with-atomic --with-program_options --with-regex toolset=clang architecture=combined cxxflags="-arch x86_64 -arch arm64" install`  
  
However when I check:  
```  
lipo -archs /Users/zhaoma/USD-21.02/release/build/boost/bin.v2/libs/program_options/build/clang-darwin-12.0/release/architecture-combined/threading-multi/visibility-hidden/libboost_program_options.dylib   
x86_64  
```  
  
I am using boost 1.72.0 any suggestions? Thank you!

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-04-22 21:25:50 UTC  
> Url: https://github.com/boostorg/build/issues/716#issuecomment-825196282  

@autoantwort run b2 with `-d+2` flag to make logs useful. A log with `--debug-building` may later be required to debug too.  
  
> Hi, is it possible that we can build universal libraries for boost?  
  
Not yet, and I am not sure if adding such a thing would be an easy task. Try contacting Apple, maybe they could offer a help to Boost.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:20 UTC  
> Url: https://github.com/boostorg/build/issues/716#issuecomment-850859582  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
