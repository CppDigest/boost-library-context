# #253 - macports build of boost 1_76 fails? [Closed]

> Username: potterpg  
> Created at: 2022-08-15 12:08:05 UTC  
> Updated at: 2022-08-15 12:55:11 UTC  
> Closed at: 2022-08-15 12:55:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/253  

I'm using macports to generate updated static libraries for a project that I now need to compile on a Mac M1 processor universal version.  
  
I'm trying to compile poppler, which has this version of boost as a requirement.  
  
Ports calls the b2 compiler with this command:  
`b2 -d2 --layout=tagged --debug-configuration --user-config=user-config.jam -sBZIP2_INCLUDE=/opt/local/include -sBZIP2_LIBPATH=/opt/local/lib -sEXPAT_INCLUDE=/opt/local/include -sEXPAT_LIBPATH=/opt/local/lib -sZLIB_INCLUDE=/opt/local/include -sZLIB_LIBPATH=/opt/local/lib -sICU_PATH=/opt/local variant=release runtime-link=shared -j8 --no-cmake-config link=shared threading=multi pch=off address-model=64 architecture=combined `  
  
Initial error reports:  
`error: 64-bit PPC compilation is not supported when targeting OSX 10.6 or later`  
  
indicating the jam file is incorrectly configured.  
  
A suggested edit on macports is in the file:  
`tools\build\src\tools\darwin.jam`  
  
is for line 463-467:  
`                if $(support-ppc64) {  
                    options = -arch x86_64 -arch ppc64 ;  
                } else {  
                    options = -arch x86_64 -arch arm64 ;  
                } `  
  
Which seems to compile further... and maybe the answer, but I guess needs putting in the live source files if its the real answer.  
  
There are still errors though? Log file attached:  
Line 46: error: No best alternative for libs/context/build/asm_sources  
Line 2357: clang: error: linker command failed with exit code 1 (use -v to see invocation)  
Line 2487: clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
[Boost_176_Compile.txt](https://github.com/boostorg/filesystem/files/9337024/Boost_176_Compile.txt)  
  
Hope this can be resolved, so a macports user can just compile this version of boost without issue.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-15 12:55:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/253#issuecomment-1214977813  

This is not a Boost.Filesystem issue. The "No best alternative" error refers to [Boost.Context](https://github.com/boostorg/context), please report the issue there. The build then fails during linking for [Boost.Coroutine](https://github.com/boostorg/coroutine) and [Boost.Fiber](https://github.com/boostorg/fiber), both of which depend on Boost.Context, so the errors are likely caused by the error of Boost.Context.  
  
If I had to guess, it is probably related to the `architecture` feature. I cannot find the `combined` value for the `architecture` feature in Boost.Build, it is only mentioned in Boost.Context. Looks like it was removed at some point. Boost.Build defines [`arm+x86`](https://github.com/bfgroup/b2/blob/7e5b03d0db4a21f74f9461e3a77d429e9d010543/src/tools/features/architecture-feature.jam#L52), and I think that is what should be specified on the command line if you want to build fat binaries for x86 and Aarch64. But `arm+x86` is not used in Boost.Context, so it may have to be updated.
