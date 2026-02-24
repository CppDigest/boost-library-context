# #259 - The testing directive "link-fail" does not work for msvc toolsets [Closed]

> Username: jeking3  
> Created at: 2017-11-05 13:30:03 UTC  
> Updated at: 2017-12-05 00:58:01 UTC  
> Closed at: 2017-12-05 00:58:01 UTC  
> Url: https://github.com/boostorg/build/issues/259  

What happened?  
  
The link-fail directive does not recognize that the link failed.  
  
Example Jamfile:  
```  
project demonstrate/msvc/link/fail/fails ;  
  
import testing ;  
  
test-suite err :  
    # this correctly catches the runtime failure and marks it (failure-as-expected)  
    [ run-fail faillink.cpp : : : : run_fail_path ]  
  
    # this does not correctly catch the link-time failure and the build fails  
    [ link-fail faillink.cpp : <define>DISABLE_AUTO_LINK : link_fail_path ]  
    ;  
```  
  
The output looks like this:  
```  
c:\boost\bjamerr>..\b2  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
...found 160 targets...  
...updating 22 targets...  
common.mkdir ..\bin.v2\bjamerr  
common.mkdir ..\bin.v2\bjamerr\run_fail_path.test  
common.mkdir ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1  
common.mkdir ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug  
common.mkdir ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32  
common.mkdir ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi  
compile-c-c++ ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\faillink.obj  
faillink.cpp  
msvc.link ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\run_fail_path.exe  
msvc.manifest ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\run_fail_path.exe  
testing.capture-output ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\run_fail_path.run  
====== BEGIN OUTPUT ======  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
(failed-as-expected) ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\run_fail_path.run  
**passed** ..\bin.v2\bjamerr\run_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\run_fail_path.test  
common.mkdir ..\bin.v2\bjamerr\link_fail_path.test  
common.mkdir ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1  
common.mkdir ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug  
common.mkdir ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32  
common.mkdir ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi  
compile-c-c++ ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\faillink.obj  
faillink.cpp  
msvc.link ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\link_fail_path.exe  
faillink.obj : error LNK2019: unresolved external symbol __imp__CryptGenRandom@12 referenced in function _main  
..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\link_fail_path.exe : fatal error LNK1120: 1 unresolved externals  
  
        call "C:\Users\JIM~1.KIN\AppData\Local\Temp\b2_msvc_14.1_vcvars32_.cmd" >nul  
link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X86 /MANIFEST /subsystem:console /out:"..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\link_fail_path.exe"   @"..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\link_fail_path.exe.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
  
...failed msvc.link ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\link_fail_path.exe ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\link_fail_path.pdb...  
...removing ..\bin.v2\bjamerr\link_fail_path.test\msvc-14.1\debug\threadapi-win32\threading-multi\link_fail_path.pdb  
...failed updating 2 targets...  
...skipped 2 targets...  
...updated 18 targets...  
```  
  
Attached sample compilation unit and Jamfile that demonstrates the issue:  
[bjamerr.zip](https://github.com/boostorg/build/files/1444067/bjamerr.zip)

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-11-06 21:36:05 UTC  
> Url: https://github.com/boostorg/build/issues/259#issuecomment-342295234  

AMDG  
  
On 11/05/2017 06:30 AM, James E. King, III wrote:  
> What happened?  
>   
> The link-fail directive does not recognize that the link failed.  
>   
  
  This happens because linking on msvc uses two separate  
actions.  I think FAIL_EXPECTED is incorrectly implemented  
to invert the exit status of each action.  
  
in Christ,  
Steven Watanabe
