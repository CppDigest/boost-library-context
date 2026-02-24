# #203 - macports build of boost 1_76 fails? [Closed]

> Username: potterpg  
> Created at: 2022-08-15 15:09:51 UTC  
> Updated at: 2023-08-18 09:36:12 UTC  
> Closed at: 2023-08-18 05:06:28 UTC  
> Url: https://github.com/boostorg/context/issues/203  

in reference to [this reported issue, but apparently on the wrong thread?](https://github.com/boostorg/filesystem/issues/253)  
Copied here:  
  
I'm using macports to generate updated static libraries for a project that I now need to compile on a Mac M1 processor universal version.  
  
I'm trying to compile poppler, which has this version of boost as a requirement.  
  
Ports calls the b2 compiler with this command:  
`b2 -d2 --layout=tagged --debug-configuration --user-config=user-config.jam -sBZIP2_INCLUDE=/opt/local/include -sBZIP2_LIBPATH=/opt/local/lib -sEXPAT_INCLUDE=/opt/local/include -sEXPAT_LIBPATH=/opt/local/lib -sZLIB_INCLUDE=/opt/local/include -sZLIB_LIBPATH=/opt/local/lib -sICU_PATH=/opt/local variant=release runtime-link=shared -j8 --no-cmake-config link=shared threading=multi pch=off address-model=64 architecture=combined`  
  
Initial error reports:  
`error: 64-bit PPC compilation is not supported when targeting OSX 10.6 or later`  
  
indicating the jam file is incorrectly configured.  
  
A suggested edit on macports is in the file:  
tools\build\src\tools\darwin.jam  
  
is for line 463-467:  
`if $(support-ppc64) { options = -arch x86_64 -arch ppc64 ; } else { options = -arch x86_64 -arch arm64 ; }`  
  
Which seems to compile further... and maybe the answer, but I guess needs putting in the live source files if its the real answer.  
  
There are still errors though? Log file attached:  
```  
Line 46: error: No best alternative for libs/context/build/asm_sources  
Line 2357: clang: error: linker command failed with exit code 1 (use -v to see invocation)  
Line 2487: clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
[Boost_176_Compile.txt](https://github.com/boostorg/filesystem/files/9337024/Boost_176_Compile.txt)  
  
Hope this can be resolved, so a macports user can just compile this version of boost without issue.  
  
A [reply on that thread](https://github.com/boostorg/filesystem/issues/253#issuecomment-1214977813) said this:  
  
This is not a Boost.Filesystem issue. The "No best alternative" error refers to [Boost.Context](https://github.com/boostorg/context), please report the issue there. The build then fails during linking for [Boost.Coroutine](https://github.com/boostorg/coroutine) and [Boost.Fiber](https://github.com/boostorg/fiber), both of which depend on Boost.Context, so the errors are likely caused by the error of Boost.Context.  
  
If I had to guess, it is probably related to the architecture feature. I cannot find the combined value for the architecture feature in Boost.Build, it is only mentioned in Boost.Context. Looks like it was removed at some point. Boost.Build defines [arm+x86](https://github.com/bfgroup/b2/blob/7e5b03d0db4a21f74f9461e3a77d429e9d010543/src/tools/features/architecture-feature.jam#L52), and I think that is what should be specified on the command line if you want to build fat binaries for x86 and Aarch64. But arm+x86 is not used in Boost.Context, so it may have to be updated.

---

## Comment 1

> Username: barracuda156  
> Created at: 2022-10-04 16:53:30 UTC  
> Updated at: 2022-11-24 06:25:45 UTC  
> Url: https://github.com/boostorg/context/issues/203#issuecomment-1267289455  

@potterpg You may want to open a ticket on Trac: https://trac.macports.org  
  
I am in fact going to deal with Boost in Macports soon, but only with regard to ppc and ppc64 (on 10.5.8 and 10.6.8). Currently `context` and `coroutine` are disabled for old systems, but those are fixable and hopefully I finalize that very soon.  
  
I am not dealing with M1 however (no hardware for that).  
  
P. S. Besides, `ppc64` will NOT build on 10.6, _not without magic._ System dylibs have no `ppc64` slices – not just in 10.6.x releases, but also in available developer builds that run on PowerPC natively.

---

## Comment 2

> Username: barracuda156  
> Created at: 2023-05-15 07:33:38 UTC  
> Url: https://github.com/boostorg/context/issues/203#issuecomment-1547334788  

UPD. `context`, `coroutine` and soon `fiber` enabled in Macports for `ppc` with Boost 1.81 (not yet `ppc64`).

---

## Comment 3

> Username: olk  
> Created at: 2023-08-18 05:06:28 UTC  
> Url: https://github.com/boostorg/context/issues/203#issuecomment-1683359002  

should work now

---

## Comment 4

> Username: potterpg  
> Created at: 2023-08-18 08:43:43 UTC  
> Url: https://github.com/boostorg/context/issues/203#issuecomment-1683574298  

Just tried: sudo port upgrade outdated  
In anticipation, but received this error:  
  
:info:build error: at libs/thread/build/Jamfile.v2:273  
:info:build error: 64-bit PPC compilation is not supported when targeting OSX 10.6 or later  
:info:build Command failed:  cd "/opt/local/var/macports/build/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_devel_boost176/boost176/work/boost_1_76_0" && /opt/local/var/macports/build/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_devel_boost176/boost176/work/boost_1_76_0/b2 -d2 --layout=tagged --debug-configuration --user-config=user-config.jam -sBZIP2_INCLUDE=/opt/local/include -sBZIP2_LIBPATH=/opt/local/lib -sEXPAT_INCLUDE=/opt/local/include -sEXPAT_LIBPATH=/opt/local/lib -sZLIB_INCLUDE=/opt/local/include -sZLIB_LIBPATH=/opt/local/lib -sICU_PATH=/opt/local variant=release runtime-link=shared -j8 --no-cmake-config link=shared threading=multi pch=off address-model=64 architecture=combined   
:info:build Exit code: 1  
:error:build Failed to build boost176: command execution failed  
:debug:build Error code: CHILDSTATUS 11765 1  
:debug:build Backtrace: command execution failed  
:debug:build     while executing  
:debug:build "system {*}$notty {*}$callback {*}$nice $fullcmdstring"  
:debug:build     invoked from within  
:debug:build "command_exec -callback portprogress::target_progress_callback build"  
:debug:build     (procedure "portbuild::build_main" line 8)  
:debug:build     invoked from within  
:debug:build "$procedure $targetname"  
:error:build See /opt/local/var/macports/logs/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_devel_boost176/boost176/main.log for details.

---

## Comment 5

> Username: potterpg  
> Created at: 2023-08-18 09:36:11 UTC  
> Url: https://github.com/boostorg/context/issues/203#issuecomment-1683640858  

Looking at:  
/opt/local/var/macports/build/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_devel_boost176/boost176/work/boost_1_76_0/tools/build/src/tools/darwin.jam  
Line 466:  
errors.user-error "64-bit PPC compilation is not supported when targeting OSX 10.6 or later" ;  
  
Which is the reported error... within this block:  
        case combined :   
        {  
            if $(address-model) = 32_64 {  
                if $(support-ppc64) {  
                    options = -arch i386 -arch ppc -arch x86_64 -arch ppc64 ;                      
                } else {  
                    # Build 3-way binary  
                    options = -arch i386 -arch ppc -arch x86_64 ;  
                }                                  
            } else if $(address-model) = 64 {  
                if $(support-ppc64) {  
                    options = -arch x86_64 -arch ppc64 ;  
                } else {  
                    errors.user-error "64-bit PPC compilation is not supported when targeting OSX 10.6 or later" ;  
                }                  
            } else {  
                options = -arch i386 -arch ppc ;  
            }  
        }  
  
Clearly the section from  else if $(address-model) = 64  
is not including M1 processors as yet...  
  
Maybe a change to this might help:  
            } else if $(address-model) = 64 {  
                if $(support-ppc64) {  
                    options = -arch x86_64 -arch ppc64 ;  
                } else {  
                    options = -arch x86_64 -arch arm64 ;  
                }  
            } else {  
This was a suggestion on another thread for Boost version 1.77
