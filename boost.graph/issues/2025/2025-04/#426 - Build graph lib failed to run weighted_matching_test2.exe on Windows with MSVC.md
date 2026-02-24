# #426 - Build graph lib failed to run weighted_matching_test2.exe on Windows with MSVC [Closed]

> Username: LinWrSpace  
> Created at: 2025-04-03 06:37:41 UTC  
> Updated at: 2025-04-30 10:37:52 UTC  
> Closed at: 2025-04-30 10:33:35 UTC  
> Url: https://github.com/boostorg/graph/issues/426  

**Issue Description:**  
Found Boost\libs\graph failed to build due to run weighted_matching_test2.exe on Windows with MSVC, this issue can be reproduced on 6f50c3c commit of Boost master branch. Could you please take a look? Thanks in advance.  
  
**Reproduce Steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git f:\Boost  
2. open a VS 2022 x64 command prompt and browse to f:\Boost  
3. .\bootstrap  
4. .\b2 headers variant=debug--build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
5. .\b2 variant=debug--build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
6. .\b2 -j16 variant=debug --build-dir=C:\gitP\boostorg\boost\amd64 libs\graph\test address-model=64  
  
[graph_test.log](https://github.com/user-attachments/files/19581301/graph_test.log)  
  
**ErrorMessage:**  
...  
msvc.link C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test2.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test2.exe  
testing.capture-output C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.run  
====== BEGIN OUTPUT ======  
libs\graph\test\weighted_matching_test.cpp(142): test 'in_file.good()' failed in function 'int __cdecl main(int,char *[])'  
1 error detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
     "C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.exe"   > "C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.output"  
    echo EXIT STATUS: %status% >> "C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.output"  
    if %status% EQU 0 (  
        copy "C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.output" "C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.run" >NUL  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\graph\test\weighted_matching_test.test\msvc-14.3\debug\x86_64\threading-multi\weighted_matching_test.run...

---

## Comment 1

> Username: jorisvr  
> Created at: 2025-04-05 09:26:37 UTC  
> Url: https://github.com/boostorg/graph/issues/426#issuecomment-2780629514  

It looks to me like the problem actually occurs in `weighted_matching_test.cpp`, not `weighted_matching_test2.cpp`.  
I suspect this test program simply fails to find its input file `weighted_matching.dat`.  
  
Previous releases of Boost Graph did not automatically run this test. I enabled this test in commit https://github.com/boostorg/graph/commit/5fa8626155642acc82d3384b16ada5e76fe292f6 but it seems that I failed to use the proper mechanism to pass the input file path. That change probably triggered your build failure.  
  
I will confirm and prepare a small pull request to fix the input file path.

---

## Comment 2

> Username: jorisvr  
> Created at: 2025-04-05 10:26:39 UTC  
> Url: https://github.com/boostorg/graph/issues/426#issuecomment-2780648633  

I verified that this issue is about the location of `weighted_matching.dat`. The linked pull request should fix it.  
  
FYI, the error is not specific to Windows. It occurs whenever the Boost Graph test suite is invoked while the current working directory is not equal to `boost/libs/graph/test`.  
  
Thanks for reporting the error.

---

## Comment 3

> Username: jorisvr  
> Created at: 2025-04-05 10:36:31 UTC  
> Url: https://github.com/boostorg/graph/issues/426#issuecomment-2780651931  

Correction: Previous releases did in fact run this test automatically. But the test would silently exit if it failed to open its input file. I "fixed" that in commit f98edae416607751b907f58ef47d6c2117c23b76 , thus triggering this issue.

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2025-04-30 10:36:45 UTC  
> Updated at: 2025-04-30 10:37:51 UTC  
> Url: https://github.com/boostorg/graph/issues/426#issuecomment-2841551584  

@jorisvr thank you so much for noticing this issue and fixing it, I really appreciate the accountability you took for the impact of the initial change.   
Now it begs the question, why didn't this problem occur in the CI?  
Oh I see, it's a matter of current working directory, right. :)
