# #820 - Boost\libs\math failed to build on MSVC [Open]

> Username: fangzhouxia  
> Created at: 2022-08-24 09:17:33 UTC  
> Updated at: 2022-08-26 06:15:40 UTC  
> Url: https://github.com/boostorg/math/issues/820  

**Issue description**:  
Boost\libs\math failed to build on MSVC. Could you please take a look? We ues https://github.com/boostorg/boost/commit/0724ce0.  
  
**Build step**:  
  
1. git clone -c core. autocrlf =true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x64 command prompt and browse to boost  
3. \bootstrap  
4. \b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. \b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. \b2 -j4 variant=release --build-dir=..\out\x64rel libs\math\test  
**Error info**:  
  
"F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.2\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.exe"   > "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.2\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.2\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output"  
    echo EXIT STATUS: %status% >> "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.2\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output"  
    if %status% EQU 0 (  
        copy "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.2\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output" "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.2\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.run"  
    `)  
**Detail log**:  
[test.log.72.txt](https://github.com/boostorg/boost/files/9414722/test.log.72.txt)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-08-24 15:57:29 UTC  
> Url: https://github.com/boostorg/math/issues/820#issuecomment-1225918726  

Which specific msvc version are you using?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-08-25 11:51:39 UTC  
> Url: https://github.com/boostorg/math/issues/820#issuecomment-1227155668  

I can reproduce with Boost-1.80 but not current develop.  Strangely neither Math not Serialization seem to have any changes in develop which might effect this.  
  
Unfortunately it'll be into next week now before I'm able to investigate further.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-08-25 17:23:05 UTC  
> Url: https://github.com/boostorg/math/issues/820#issuecomment-1227555450  

OK, I did look at this after all: @mclow this is a packaging issue: the directory libs/math/example/.temps is not present in the distribution, and this causes std::ofstream to fail to open.  Do I need to change the name of the directory or is there a better way?

---

## Comment 4

> Username: fangzhouxia  
> Created at: 2022-08-26 06:15:40 UTC  
> Url: https://github.com/boostorg/math/issues/820#issuecomment-1228097395  

> **Detail log**:  
  
We use the latest internal build. The specific version is as follows:  
![image](https://user-images.githubusercontent.com/109721669/186834953-0f71f087-3829-4f41-a5de-9a5b585f0208.png)
