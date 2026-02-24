# #35 - Compile problems on Android ndk r16 beta 1 [Closed]

> Username: berkus  
> Created at: 2017-10-25 12:54:08 UTC  
> Updated at: 2017-10-25 13:24:49 UTC  
> Closed at: 2017-10-25 13:24:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/35  

Hello, in attempt to build boost for android with more or less reasonable strict settings (-Werror) I uncovered some bugs.  
  
```  
clang-darwin.compile.c++ android-build/boost/bin.v2/   
libs/program_options/build/clang-darwin-5.0~x86/debug/   
address-model-32/link-stati   
c/target-os-android/threading-multi/cmdline.o   
libs/program_options/src/cmdline.cpp:104:41: error: declaration shadows a   
field of 'boost::program_options::detail::cmdline' [-Werror   
,-Wshadow]   
    cmdline::init(const vector<string>& args)   
                                        ^   
./boost/program_options/detail/cmdline.hpp:139:34: note: previous   
declaration is here   
        std::vector<std::string> args;   
                                 ^   
libs/program_options/src/cmdline.cpp:508:48: error: declaration shadows a   
field of 'boost::program_options::detail::cmdline' [-Werror   
,-Wshadow]   
    cmdline::parse_long_option(vector<string>& args)   
                                               ^   
./boost/program_options/detail/cmdline.hpp:139:34: note: previous   
declaration is here   
        std::vector<std::string> args;   
                                 ^   
libs/program_options/src/cmdline.cpp:545:49: error: declaration shadows a   
field of 'boost::program_options::detail::cmdline' [-Werror,-Wshadow]   
    cmdline::parse_short_option(vector<string>& args)   
                                                ^   
./boost/program_options/detail/cmdline.hpp:139:34: note: previous   
declaration is here   
        std::vector<std::string> args;   
                                 ^   
libs/program_options/src/cmdline.cpp:611:47: error: declaration shadows a   
field of 'boost::program_options::detail::cmdline' [-Werror,-Wshadow]   
    cmdline::parse_dos_option(vector<string>& args)   
                                              ^   
./boost/program_options/detail/cmdline.hpp:139:34: note: previous   
declaration is here   
        std::vector<std::string> args;   
                                 ^   
libs/program_options/src/cmdline.cpp:632:58: error: declaration shadows a   
field of 'boost::program_options::detail::cmdline' [-Werror,-Wshadow]   
    cmdline::parse_disguised_long_option(vector<string>& args)   
                                                         ^   
./boost/program_options/detail/cmdline.hpp:139:34: note: previous   
declaration is here   
        std::vector<std::string> args;   
                                 ^   
libs/program_options/src/cmdline.cpp:663:47: error: declaration shadows a   
field of 'boost::program_options::detail::cmdline' [-Werror,-Wshadow]   
    cmdline::parse_terminator(vector<string>& args)   
                                              ^   
./boost/program_options/detail/cmdline.hpp:139:34: note: previous   
declaration is here   
        std::vector<std::string> args;   
                                 ^   
libs/program_options/src/cmdline.cpp:683:55: error: declaration shadows a   
field of 'boost::program_options::detail::cmdline' [-Werror,-Wshadow]   
    cmdline::handle_additional_parser(vector<string>& args)   
                                                      ^   
./boost/program_options/detail/cmdline.hpp:139:34: note: previous   
declaration is here   
        std::vector<std::string> args;   
                                 ^   
7 errors generated.   
    "/usr/local/opt/android-ndk/android-ndk-r16-beta1//   
toolchains/llvm/prebuilt/darwin-x86_64/bin/clang++"   
"-DBOOST_AC_USE_PTHREADS" "-DBOOST_SP_USE_PTHREADS" "-fvisibility=hidden"   
"-fvisibility-inlines-hidden" "-Wno-unused-local-typedef" -x c++ -std=c++14   
-O0 -g -O0 -fno-inline -Wall -g --target=i686-none-linux-android   
--gcc-toolchain=/usr/local/opt/android-ndk/android-ndk-   
r16-beta1//toolchains/x86-4.9/prebuilt/darwin-x86_64   
--sysroot=/usr/local/opt/android-ndk/android-ndk-r16-beta1//sysroot   
-isystem /usr/local/opt/android-ndk/android-ndk-r16-beta1//   
sources/cxx-stl/llvm-libc++/include -isystem /usr/local/opt/android-ndk/   
android-ndk-r16-beta1//sources/cxx-stl/llvm-libc++abi/include -isystem   
/usr/local/opt/android-ndk/android-ndk-r16-beta1//sources/android/support/include   
-isystem /usr/local/opt/android-ndk/android-ndk-r16-beta1//sysroot/usr/include   
-isystem /usr/local/opt/android-ndk/android-ndk-r16-beta1//   
sysroot/usr/include/i686-linux-android -DANDROID -D__ANDROID_API__=21   
-ffunction-sections -funwind-tables -fstack-protector-strong   
-fno-limit-debug-info -fPIC -no-canonical-prefixes -mstackrealign   
-Wa,--noexecstack -Wformat -Werror=format-security -Wall -Werror -Wshadow   
-march=i686 -DBOOST_ALL_NO_LIB=1 -D_LITTLE_ENDIAN -I"." -c -o   
"android-build/boost/bin.v2/libs/program_options/build/   
clang-darwin-5.0~x86/debug/address-model-32/link-static/   
target-os-android/threading-multi/cmdline.o" "libs/program_options/src/   
cmdline.cpp"   
```  
  
This is using `clang` toolchain on `Android NDK r16 beta1`.  
  
[​Reference to user-config.jam for the build](https://github.com/twilio/twilio-boost-build/blob/android-ndk-16/boost.sh#L526)

---

## Comment 1

> Username: berkus  
> Created at: 2017-10-25 13:05:24 UTC  
> Url: https://github.com/boostorg/program_options/issues/35#issuecomment-339323080  

Xref https://svn.boost.org/trac10/ticket/13273
