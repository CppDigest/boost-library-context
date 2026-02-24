# #143 - missing Boost.Fiber binaries for mingw-w64 builds on Windows [Closed]

> Username: sav-ix  
> Created at: 2017-08-27 08:01:41 UTC  
> Updated at: 2017-10-26 06:35:55 UTC  
> Closed at: 2017-10-26 06:35:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/143  

Hello everyone,  
  
For Boost builds using mingw-w64 got message:  
```  
    - fiber                    : building  
```  
during configuration, but   
```  
lib/boost_fiber-mgw72-mt-1_65.dll  
lib/boost_fiber-mgw72-mt-1_65.dll.a  
```  
binaries are missing in Boost installation directory.  
  
Environment:  
* Windows 10 x64,  
* mingw-w64 <6.3.0|7.2.0> x86_64,  
* [Boost-1.65.0](https://dl.bintray.com/boostorg/release/1.65.0/source/boost_1_65_0.tar.gz).  
  
Reproduced for:  
* [Boost-1.65.0](https://dl.bintray.com/boostorg/release/1.65.0/source/boost_1_65_0.tar.gz) builds using mingw-w64 with `<Debug|Release><Shared|Static>` configurations,  
  
not reproduced for:  
* builds using MSVC,  
* [Boost-1.65.0-beta1_rc2](https://dl.bintray.com/boostorg/beta/1.65.0.beta.1/source/boost_1_65_0_beta1_rc2.tar.gz) builds using mingw-w64.  
  
UPDATE: is a result of PR# https://github.com/boostorg/config/commit/fe5e07b521e49f6cca712c801e025fed13c23979; since not a Boost.Fiber bug, **please close this issue**.  
  
  
Alexander

---

## Comment 1

> Username: olk  
> Created at: 2017-10-26 06:35:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/143#issuecomment-339566060  

thx
