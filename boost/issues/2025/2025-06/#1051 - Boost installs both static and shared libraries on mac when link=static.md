# #1051 - [1.88.0] Boost installs both static and shared libraries on mac when link=static [Closed]

> Username: uilianries  
> Created at: 2025-06-13 11:58:05 UTC  
> Updated at: 2025-10-30 15:44:09 UTC  
> Closed at: 2025-10-30 15:44:09 UTC  
> Url: https://github.com/boostorg/boost/issues/1051  

Greetings, fellow Boosters!  
  
I noted when building Boost 1.88.0, and installing their libraries when using Conan, the version 1.88.0, and only this specific version is installing a few .dylib with their .a altogether.   
  
The CI build log can be found here: https://c3i.jfrog.io/artifactory/cci-build-logs/cci/prod/PR-27526/3/package_build_logs/build_log_boost_1_88_0_02627a4b63c60cef7daff8b936b121ff_7a20febe5394559edf1d4111f529a5e35161a75e.success.txt  
  
```  
boost/1.88.0: package(): Packaged 1 '.txt' file: LICENSE_1_0.txt  
boost/1.88.0: package(): Packaged 15546 '.hpp' files  
boost/1.88.0: package(): Packaged 151 '.h' files  
boost/1.88.0: package(): Packaged 293 '.ipp' files  
boost/1.88.0: package(): Packaged 2 '.inc' files: strict_cpp_re.inc, cpp_re.inc  
boost/1.88.0: package(): Packaged 44 '.a' files  
boost/1.88.0: package(): Packaged 4 '.dylib' files: libboost_thread.dylib, libboost_charconv.dylib, libboost_system.dylib, libboost_chrono.dylib  
```  
  
Of course, it does not help much, as Conan does a lot of things. But don't worry, that bug is easily reproducible using a regular build.  
  
This behavior **only** occurs in Mac. I tested on both Linux and Windows, and no extra dynamic library is installed.  
  
#### Environment  
  
OS: Apple MacOS 15.3.2 (24D81)  
Architecture: ARMv8 Apple M1 Max  
Compiler: Apple clang version 17.0.0 (clang-1700.0.13.5) - Target: arm64-apple-darwin24.3.0  
  
  
#### Steps to reproduce  
  
```  
wget https://archives.boost.io/release/1.88.0/source/boost_1_88_0.tar.bz2  
tar jxf boost_1_88_0.tar.bz2  
cd boost_1_88_0/  
./bootstrap.sh  
./b2 link=static install --prefix=/tmp/install-boost  
ls /tmp/install-boost/lib  
```  
  
#### Actual Behavior  
  
After building Boost 1.88.0 with explicit static library only, I can see the follow installed libraries:  
  
```  
ls /tmp/install-boost/lib                                                                                                                                                                                                   
cmake/                           libboost_context.a               libboost_graph.a                 libboost_numpy310.a              libboost_serialization.a         libboost_thread.a                libboost_wserialization.a  
libboost_atomic.a                libboost_contract.a              libboost_iostreams.a             libboost_prg_exec_monitor.a      libboost_stacktrace_addr2line.a  libboost_thread.dylib*  
libboost_charconv.a              libboost_coroutine.a             libboost_json.a                  libboost_process.a               libboost_stacktrace_basic.a      libboost_timer.a  
libboost_charconv.dylib*         libboost_date_time.a             libboost_locale.a                libboost_program_options.a       libboost_stacktrace_noop.a       libboost_type_erasure.a  
libboost_chrono.a                libboost_exception.a             libboost_log.a                   libboost_python310.a             libboost_system.a                libboost_unit_test_framework.a  
libboost_chrono.dylib*           libboost_fiber.a                 libboost_log_setup.a             libboost_random.a                libboost_system.dylib*           libboost_url.a  
libboost_container.a             libboost_filesystem.a            libboost_nowide.a                libboost_regex.a                 libboost_test_exec_monitor.a     libboost_wave.a  
```  
  
Boost chrono, charconv, system and thread are producing .dylib as well.  
  
#### Expected Behavior  
  
The B2 property `link=static` should result in installing **only** static libraries. No .dylib should be built/installed.  
  
Reference: https://www.bfgroup.xyz/b2/manual/main/index.html#b2.overview.invocation.properties  
  
#### Build Log  
  
The following build log file shows the "steps to reproduce" result:  
  
[boost-1.88.0-static.log](https://github.com/user-attachments/files/20726168/boost-1.88.0-static.log)

---

## Comment 1

> Username: soekkle  
> Created at: 2025-07-16 10:30:36 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3077946192  

Hi everone,  
  
I think, I can reproduce the same issue with conan on a Debian Trixie machine. there a a few .so.1.88.0 libs.  
  
Best regards  
  
soekkle

---

## Comment 2

> Username: Nekto89  
> Created at: 2025-07-19 14:19:51 UTC  
> Updated at: 2025-07-19 14:29:29 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3092376262  

> Hi everone,  
>   
> I think, I can reproduce the same issue with conan on a Debian Trixie machine. there a a few .so.1.88.0 libs.  
>   
> Best regards  
>   
> soekkle  
  
I've reproduced it with Boost.Locale and ICU backend on Ubuntu 16.04 (Docker image conanio/gcc11-ubuntu16.04:2.18.1)  
```  
./b2 boost.locale.icu=on boost.locale.iconv=off --disable-iconv link=static variant=release --with-locale cxxstd=17 install -sICU_PATH=/home/conan/.conan2/p/icucfab22a0683c2/p linkflags=-ldl --prefix=/tmp/install-boost > /usr/src/log_boost.txt 2>&1  
  
ls /tmp/install-boost/lib  
cmake                        libboost_chrono.a          libboost_system.a          libboost_thread.so  
libboost_charconv.a          libboost_chrono.so         libboost_system.so         libboost_thread.so.1.88.0  
libboost_charconv.so         libboost_chrono.so.1.88.0  libboost_system.so.1.88.0  
libboost_charconv.so.1.88.0  libboost_locale.a          libboost_thread.a  
  
```  
  
[boost_locale_1_88_0.txt](https://github.com/user-attachments/files/21328216/boost_locale_1_88_0.txt)

---

## Comment 3

> Username: uilianries  
> Created at: 2025-07-21 05:41:44 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3095281390  

@Nekto89 I can confirm your scenario too. I also can reproduce your case in Linux as well:   
  
[boost-1.88.0-linux-x86_64-rel-gcc11-static.log](https://github.com/user-attachments/files/21340985/boost-1.88.0-linux-x86_64-rel-gcc11-static.log)  
  
Thank you for commenting it!

---

## Comment 4

> Username: uilianries  
> Created at: 2025-07-21 06:30:45 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3095416042  

Just checked on Windows and it works fine, no duplicated libraries:  
  
[boost-1.88.0-win-x86_64-rel-static.log](https://github.com/user-attachments/files/21341435/boost-1.88.0-win-x86_64-rel-static.log)

---

## Comment 5

> Username: Nekto89  
> Created at: 2025-09-01 08:20:24 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3241360561  

1.89.0 has even more dynamic libraries  
```  
wget https://archives.boost.io/release/1.89.0/source/boost_1_89_0.tar.bz2  
tar jxf boost_1_89_0.tar.bz2  
cd boost_1_89_0/  
./bootstrap.sh  
./b2 boost.locale.icu=on boost.locale.iconv=off --disable-iconv link=static variant=release --with-locale cxxstd=17 install -sICU_PATH=/home/conan/.conan2/p/icu9f28dec67c565/p linkflags=-ldl --prefix=/tmp/install-boost > /usr/src/log_boost_1_89.txt 2>&1  
ls /tmp/install-boost/lib  
cmake                      libboost_charconv.so.1.89.0  libboost_container.so.1.89.0  libboost_thread.a  
libboost_atomic.a          libboost_chrono.a            libboost_date_time.a          libboost_thread.so  
libboost_atomic.so         libboost_chrono.so           libboost_date_time.so         libboost_thread.so.1.89.0  
libboost_atomic.so.1.89.0  libboost_chrono.so.1.89.0    libboost_date_time.so.1.89.0  
libboost_charconv.a        libboost_container.a         libboost_exception.a  
libboost_charconv.so       libboost_container.so        libboost_locale.a  
```  
  
[log_boost_1_89.txt](https://github.com/user-attachments/files/22073850/log_boost_1_89.txt)

---

## Comment 6

> Username: grisumbras  
> Created at: 2025-10-20 15:28:10 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3422585980  

https://github.com/boostorg/locale/pull/266 should fix this

---

## Comment 7

> Username: Flamefire  
> Created at: 2025-10-30 15:28:45 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3468606685  

https://github.com/boostorg/locale/pull/266 is merged, so this can be closed now

---

## Comment 8

> Username: uilianries  
> Created at: 2025-10-30 15:44:09 UTC  
> Url: https://github.com/boostorg/boost/issues/1051#issuecomment-3468679808  

Closing as the fix is confirmed: https://github.com/boostorg/locale/pull/266#issuecomment-3424875222  
  
Thank you for all your support!
