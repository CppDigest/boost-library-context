# #220 - One test fails to compile after boostorg/msm submodule graph updated from 65a65c7 to 3cabf1a boost/graph @ 554da29 [Open]

> Username: 215020267  
> Created at: 2020-05-28 08:22:43 UTC  
> Updated at: 2022-06-14 01:39:38 UTC  
> Url: https://github.com/boostorg/graph/issues/220  

**Environment:**  
VS 2019 + Windows Server 2016  
**Issue description:**  
One test fails to compile after boostorg/msm submodule graph updated from 65a65c7 to 3cabf1a boost/graph @ 554da29. Could you please take a look?  
  
Reproduce steps:  
git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
open a VS 2017 x64 command prompt and browse to D:\Boost\src  
.\bootstrap  
.\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
.\b2 variant=release --build-dir=..\out\x64rel address-model=64  
.\b2 -j8 variant=release --build-dir=..\out\amd64rel libs\graph\test  
[test.log.39.log](https://github.com/boostorg/graph/files/4694243/test.log.39.log)  
  
**ErrorMessage:**  
EXIT STATUS: 255   
====== END OUTPUT ======  
  
    set Path=F:\gitP\boostorg\out\amd64rel\boost\bin.v2\libs\chrono\build\msvc-14.2\release\threading-multi;F:\gitP\boostorg\out\amd64rel\boost\bin.v2\libs\timer\build\msvc-14.2\release\threading-multi;%Path%  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.exe" 10000  > "..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.output"  
    echo EXIT STATUS: %status% >> "..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.output"  
    if %status% EQU 0 (  
        copy "..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.output" "..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
...failed testing.capture-output ..\out\amd64rel\boost\bin.v2\libs\graph\test\dijkstra_heap_performance.test\msvc-14.2\release\threading-multi\dijkstra_heap_performance.run...

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-04-20 17:49:15 UTC  
> Url: https://github.com/boostorg/graph/issues/220#issuecomment-823478908  

Current develop and master are passing that test with msvc-14.2.  Are you still seeing this?

---

## Comment 2

> Username: QuellaZhang  
> Created at: 2022-06-10 02:45:47 UTC  
> Url: https://github.com/boostorg/graph/issues/220#issuecomment-1151864269  

@jzmaddock  This issue still can be reproduced on VS2019 16.11.11.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2022-06-14 01:39:38 UTC  
> Url: https://github.com/boostorg/graph/issues/220#issuecomment-1154616913  

@QuellaZhang , can you make a pull request that demonstrates the problem? If necessary, add the appropriate version of MSVC to the `ci.yml` file. Thanks.
