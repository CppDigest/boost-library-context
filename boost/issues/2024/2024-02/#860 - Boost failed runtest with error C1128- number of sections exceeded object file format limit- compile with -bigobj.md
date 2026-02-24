# #860 - Boost failed runtest with error C1128: number of sections exceeded object file format limit: compile with /bigobj [Open]

> Username: WillaCao  
> Created at: 2024-02-08 06:37:08 UTC  
> Updated at: 2024-02-08 06:37:08 UTC  
> Url: https://github.com/boostorg/boost/issues/860  

### Description  
The issue only repro on X64-bit. Adding the /bigobj option works. Could you please help to take a look at this? Thank you.  
  
### Steps to Reproduce  
1. Open vs2022 x64 native Tools Command Prompt  
2. git clone https://github.com/boostorg/boost F:\gitP\boostorg\boost  
3. cd F:\gitP\boostorg\boost  
4. git reset --hard ad09f66  
5.  .\bootstrap  
6.  .\b2 headers variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64  
7.  .\b2 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64  
8.  set _CL_=/D_DISABLE_CONSTEXPR_MUTEX_CONSTRUCTOR /D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING /DBOOST_TIMER_ENABLE_DEPRECATED /wd4996 %_CL_%  
9.  set OPENSSL_ROOT=F:\tools\OpenSSL\64  
10.  set path=F:\tools\OpenSSL\64\bin;%path%  
11.  set path=C:\Python\Python27;%path%  
12.  .\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\type_erasure\test address-model=64  
  
### Expected behavior  
Run tests all pass  
  
### Actual behavior  
error C1128: <func:#0, Mod:F:\gitP\boostorg\boost\libs\type_erasure\test\test_assign.cpp> number of sections exceeded object file format limit: compile with /bigobj  
[test.log.103.log](https://github.com/boostorg/boost/files/14205079/test.log.103.log)
