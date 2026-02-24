# #963 - [MSVC] Submodule filesystem test failed due to memory access violation [Closed]

> Username: brianGriifin114  
> Created at: 2024-10-11 03:02:27 UTC  
> Updated at: 2024-10-11 06:14:21 UTC  
> Closed at: 2024-10-11 06:14:21 UTC  
> Url: https://github.com/boostorg/boost/issues/963  

Hi, we are part of the MSVC test team. Recently, we encountered a memory access violation when running the boost test module.  
According to the log, the following tests failed and resulted in the same exit status: -1073741819 (access violation error):  
  
operations_test_static.run (static link version)  
operations_test_v3.run  
operations_test.run  
The exit status of these tests is -1073741819, indicating that memory access violation problems were encountered in these tests. Plz refer test.log for more detail.  
[Test.log](https://github.com/user-attachments/files/17337116/Test.log)  
[filesystem-Test.log](https://github.com/user-attachments/files/17336957/filesystem-Test.log)  
  
  
**Steps to repro:**  
  
1. Open VS2022 x64 Native Tools command.  
2. git clone https://github.com/boostorg/boost.git  
3. cd boostorg\boost  
4. git fetch --recurse-submodules=no --force  
5. git -C "C:\gitP\boostorg\boost" submodule sync  
6. git submodule foreach git reset --hard  
7. git submodule foreach git clean -xdf  
8. git submodule update --init --recursive  
9. set _CL_=/DBOOST_TIMER_ENABLE_DEPRECATED %_CL_%  
10. .\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\filesystem\test address-model=64 2>&1
