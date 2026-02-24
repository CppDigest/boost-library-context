# #610 - One test "strategies_distance_cross_track.exe" failed with runtime failure on debug and release Configuration. [Closed]

> Username: spacelg  
> Created at: 2019-07-12 06:23:11 UTC  
> Updated at: 2019-11-06 15:37:30 UTC  
> Closed at: 2019-11-06 15:37:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/610  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test "strategies_distance_cross_track.exe" failed with runtime failure on debug and release Configuration. Could you please take a look?  
  
Reproduce steps:.  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\geometry\test  
  
[log_x64_test_42.log](https://github.com/boostorg/geometry/files/3385205/log_x64_test_42.log)  
  
ErrorMessage:  
====== BEGIN OUTPUT ======  
Running 1 test case...  
libs/geometry/test/strategies/distance_cross_track.cpp(32): error: in "test_main_caller( argc, argv )": result {12.89865453450027033} different than expected {12.898977232559222017}.. 2.5018e-05  
libs/geometry/test/strategies/distance_cross_track.cpp(32): error: in "test_main_caller( argc, argv )": result {7.3895682690065029874} different than expected {7.3883629173067344453}.. 0.000163142  
  
*** 2 failures are detected in the test module "Test Program"

---

## Comment 1

> Username: vissarion  
> Created at: 2019-07-15 12:09:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/610#issuecomment-511376196  

Thanks for pointing this out. Please let me know if this is fixed here https://github.com/vissarion/geometry/tree/fix/distance_accuracy

---

## Comment 2

> Username: yuxianch  
> Created at: 2019-10-23 03:05:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/610#issuecomment-545243992  

Hi @vissarion , it seems that your commit does not fix this issue. After changing "0.0000001" to "0.001", this test still got above runfail.

---

## Comment 3

> Username: yuxianch  
> Created at: 2019-10-23 03:15:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/610#issuecomment-545246185  

Changing "0.0000001" to "0.1", this test will have no error.

---

## Comment 4

> Username: vissarion  
> Created at: 2019-10-31 16:22:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/610#issuecomment-548455923  

Ok changed here https://github.com/boostorg/geometry/pull/634 for now, in the future we should probably allow larger errors for smaller distances.
