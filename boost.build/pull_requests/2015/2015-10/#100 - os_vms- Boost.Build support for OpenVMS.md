# #100 os_vms: Boost.Build support for OpenVMS [Merged]

> Username: nomadbyte  
> Created at: 2015-10-28 06:35:52 UTC  
> Updated at: 2021-10-02 22:20:10 UTC  
> Merged at: 2015-12-14 08:03:56 UTC  
> Closed at: 2015-12-14 08:03:56 UTC  
> Url: https://github.com/boostorg/build/pull/100  

`os_vms` branch re-integrates Boost.Build engine support for OpenVMS and   
adds `vmsdecc` toolset to use the build-system with OpenVMS HP C/C++ compilers.  
  
This implementation allows the use of POSIX-style path on VMS (unlike the  
previously phased-out version). This streamlines portability of jam-projects and   
simplifies integration, often with no changes needed.  
  
The resulting implementation was tested against the current suite of Boost.Build  
tests [*].  
  
**BUILD AND INSTALL**  
  
Download the Boost.Build source from repository in zip format.  
To bootstrap and install Boost.Build into target root directory on OpenVMS:  
  
```  
$ unzip -a boost-build.zip  
$ @bootstrap_vms  
$ define root dev:[root_dir.] /trans=concealed  
$ mc []b2.exe --prefix="/root" install  
$ b2 :== $root:[bin]b2.exe  
```  
  
NOTE: To pass parameters that contain capital letters, enclose the whole parameter  
expression in double-quotes (e.g. `"-sBOOST_BUILD_PATH=/b2_root"` )  
  
**EXAMPLES**  
  
By default the Boost.Build examples are installed along with the core files into  
installation tree. E.g. to test the `libraries` example project:  
  
```  
$ backup root:[share.boost-build.example.libraries...]  [.libraries...]  
$ set def [.libraries]  
$ b2 toolset=vmsdecc link=static release  
```  
  
**CHANGES**  
  
Briefly about the implemented changes:  
1. Updates to `bjam`-engine:  
   - POSIX-style path handling on OpenVMS, translation to VMS-style path in jam-file  
     context via `:W` modifier (e.g. `$(path:W)`)  
   - VMS-specific command execution and exit status handling  
   - VMS support for accessing environment variables (from `.ENVIRON` module)  
   - Add bootstrap script for VMS  
2. Added `GLOB_ARCHIVE` built-in rule to glob archive members by name:  
   - Support for such functionality has long been present in the bjam-engine;  
     so made it useable via a built-in rule, and expanded it for use on VMS --  
     needed for `vmsdecc` toolset.  
   - Added `builtin_glob_archive` test case to Boost.Build test suite  
3. Updates to Boost.Build build system jam-files:  
   - POSIX-style path handling on VMS  
   - VMS-specific actions  
4. Added `vmsdecc` toolset to use with OpenVMS HP C/C++ compilers  
5. Updates to `bjam` documentation about the implemented changes  
  
[*] **TESTS**  
  
Bulk of the implemented changes is VMS-specific and should be transparent on other  
platforms. The resulting implementation was tested against the current  
Boost.Build test suite. On non-VMS platforms (linux, windows) `os_vms` test results  
fully match those for the base branch. To test on VMS, only VMS supported  
test-cases were included.  
  
Test results on VMS (total tests: 128) :  
- PASSED: 110  
- FAILED: 4  
- NOT-SUPPORTED: 14 [**]  
  
[**] **KNOWN ISSUES**  
  
All of the listed issues are **VMS-specific** and are not present on other  
platforms.  
1. File names with spaces or multiple dots are not supported currently.  
2. File names with null-suffix (e.g. `"file"` vs. `"file."`) are ambiguous and  
   assumed to be directory names. For files -- specify at least the trailing dot.  
3. Running a debug version of an executable launches the VMS Debugger. This is a  
   default system behavior and may result in a `DBG>` prompt presented from build  
   sub-process. To avoid this, especially when using `testing` module, build   
   `release` variant (also `link=static`).  
4. Shared-library variants by default do not contain any symbols. On VMS, a link  
   options file is needed to explicitly list exported symbols.  
   Use `link=static` instead.  
5. Parallel actions are not supported currently. The make actions are executed  
   in `system()` call, which is effectively blocking/sequential.  
6. When requesting debug-options ( `-d1`, `-d+2` etc.), action's output is reported  
   before the action's info line. This is due to use of `system()` to execute make  
   commands, this does not allow redirection of sub-process' `stderr` and `stdout`.  
   Actions are executed in correct order nevertheless.  
7. Excessively long directory paths may break the builds. DCL has limits on   
   command line length, yet Boost.Build uses verbose and structured naming  
   conventions which may result in long path-names. When possible, define a  
   concealed logical device for the top of the project root.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-11-19 17:55:35 UTC  
> Url: https://github.com/boostorg/build/pull/100#issuecomment-158136413  

Artur,  
  
thanks for the contribution! Testing on Ubuntu shows no regression for me; I'd be happy to merge when you comment on the Windows question.

---

## Comment 2

> Username: vprus  
> Created_at: 2015-12-14 08:04:33 UTC  
> Url: https://github.com/boostorg/build/pull/100#issuecomment-164373130  

Artur,  
  
I have merged this patch series, thanks!

---
