# #329 - [MSVC] Submodule filesystem test failed due to memory access violation [Closed]

> Username: brianGriifin114  
> Created at: 2024-10-11 06:14:32 UTC  
> Updated at: 2024-10-12 06:07:06 UTC  
> Closed at: 2024-10-12 06:07:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/329  

Hi, we are part of the MSVC test team. Recently, we encountered a memory access violation when running the boost test module.  
According to the log, the following tests failed and resulted in the same exit status: -1073741819 (access violation error):  
  
operations_test_static.run (static link version)  
operations_test_v3.run  
operations_test.run  
The exit status of these tests is -1073741819, indicating that memory access violation problems were encountered in these tests. Plz refer test.log for more detail.  
[Test.log](https://github.com/user-attachments/files/17337116/Test.log)  
[filesystem-Test.log](https://github.com/user-attachments/files/17336957/filesystem-Test.log)  
  
Steps to repro:  
  
Open VS2022 x64 Native Tools command.  
git clone https://github.com/boostorg/boost.git  
cd boostorg\boost  
git fetch --recurse-submodules=no --force  
git -C "C:\gitP\boostorg\boost" submodule sync  
git submodule foreach git reset --hard  
git submodule foreach git clean -xdf  
git submodule update --init --recursive  
set CL=/DBOOST_TIMER_ENABLE_DEPRECATED %CL%  
.\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\filesystem\test address-model=64 2>&1

---

## Comment 1

> Username: Lastique  
> Created at: 2024-10-11 12:16:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/329#issuecomment-2407290520  

I cannot reproduce this locally with the current develop on MSVC 14.3 (cl version 19.41.34123, VS2022 version 17.11.5). We also run this compiler in our [CI](https://github.com/boostorg/filesystem/actions/runs/11160569172/job/31021290412) and it also passes.  
  
Are you running some pre-release version of MSVC? If so, you should contact the compiler devs first.  
  
If not, you will have to investigate on your end. For a start, I would need a backtrace or a more detailed description of what is going on prior to the crash. You might want to try building the debug version for that.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-10-11 12:23:17 UTC  
> Updated at: 2024-10-11 12:25:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/329#issuecomment-2407302946  

BTW, `set CL=/DBOOST_TIMER_ENABLE_DEPRECATED %CL%` doesn't work for me because `CL` is not defined originally. And presumably, `%CL%` should go before the `/D` argument. If you want to define the `BOOST_TIMER_ENABLE_DEPRECATED` macro then add `define=BOOST_TIMER_ENABLE_DEPRECATED` to the `b2` command line. But this macro is irrelevant to Boost.Filesystem tests anyway.

---

## Comment 3

> Username: brianGriifin114  
> Created at: 2024-10-12 06:07:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/329#issuecomment-2408410558  

Through our own investigation, we found that this is a problem with the pre-release version of MSVC. This problem will not occur when using the released version. Thank you for your attention. This issue has been provided to the MSVC development team to avoid it in subsequent releases.
