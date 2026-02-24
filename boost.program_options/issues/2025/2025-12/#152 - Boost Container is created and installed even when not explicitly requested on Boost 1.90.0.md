# #152 - [bug] Boost Container is created and installed even when not explicitly requested on Boost 1.90.0 [Closed]

> Username: uilianries  
> Created at: 2025-12-23 11:12:45 UTC  
> Updated at: 2026-02-19 20:00:54 UTC  
> Closed at: 2026-02-19 20:00:54 UTC  
> Url: https://github.com/boostorg/program_options/issues/152  

Hello!  
  
### Summary  
  
When building Boost 1.90.0 with a minimal environment, asking to build only `program_options`, Boost will actually build and install both `program_options` and `container` modules. It seems to be a transitive module asking for `container` and generating it. This scenario only happens for Boost 1.90.0, the previous version 1.89.0 does not result in this bug.  
  
This report comes from https://github.com/conan-io/conan-center-index/issues/29193  
  
### Environment  
  
Boost: 1.90.0 (https://archives.boost.io/release/1.90.0/source/boost_1_90_0.tar.bz2)  
OS: GNU/Linux 6.8.0  
Distro: Ubuntu 22.04  
Compiler: gcc (Ubuntu 11.4.0-1ubuntu1~22.04.2) 11.4.0  
  
### Details  
  
Locally, I can obtain the described result on Linux, but I have a report about the same scenario is possible on Apple OSX as well.  
  
#### Steps to Reproduce  
  
```  
wget https://archives.boost.io/release/1.90.0/source/boost_1_90_0.tar.bz2  
tar jxf boost_1_90_0.tar.bz2  
cd boost_1_90_0/  
./bootstrap.sh --with-toolset=gcc --prefix=/tmp/boost-1.90.0/install  
./b2 --layout=system --with-program_options install  --prefix=/tmp/boost-1.90.0/install  
ls /tmp/boost-1.90.0/install/lib/  
```  
  
The same steps can be reused for Boost 1.89.0, where `libboost_container.a` library will **not** be installed as expected.   
  
#### Build logs  
  
- Linux + Boost 1.90.0 + program-options only (bug): [boost-1.90.0-linux-program-options.log](https://github.com/user-attachments/files/24309766/boost-1.90.0-linux-program-options.log)  
  
- Linux + Boost 1.89.0 + program-options only (no bug): [boost-1.89.0-linux-program-options.log](https://github.com/user-attachments/files/24309808/boost-1.89.0-linux-program-options.log)  
  
Regards.

---

## Comment 1

> Username: vprus  
> Created at: 2025-12-25 18:41:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/152#issuecomment-3691664838  

I don't remember any changes that would have had this effect. That means the change is somewhere else in the build infrastructure, and I would not be able to investigate it in any observable future, I'm afraid.   
  
Somebody else would have to pick it up.
