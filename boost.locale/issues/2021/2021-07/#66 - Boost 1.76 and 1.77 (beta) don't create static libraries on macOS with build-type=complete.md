# #66 - Boost 1.76 and 1.77 (beta) don't create static libraries on macOS with build-type=complete [Open]

> Username: lazar-ivanov  
> Created at: 2021-07-18 13:34:37 UTC  
> Updated at: 2026-01-26 21:40:31 UTC  
> Url: https://github.com/boostorg/locale/issues/66  

Boost 1.76 and 1.77 Boost.Locale doesn't create the static libraries (s, sd flavors) with build-type=complete on macOS (Mojave, Bug Sur, both arm & intel). I'm using boost.locale.icu=off boost.locale.iconv=on parameters and you can see the complete b2 command below.  
  
On Boost 1.75 it works and creates the following libraries:  
  
libboost_locale-mt-a64.a  
libboost_locale-mt-a64.dylib  
libboost_locale-mt-d-a64.a  
libboost_locale-mt-d-a64.dylib  
libboost_locale-mt-s-a64.a  
libboost_locale-mt-sd-a64.a  
  
On Boost 1.76 and 1.77 does not create the static versions (sd & s flavors), but just the dynamic versions:  
  
libboost_locale-mt-a64.a  
libboost_locale-mt-a64.dylib  
libboost_locale-mt-d-a64.a  
libboost_locale-mt-d-a64.dylib  
  
The complete b2 command used is the following:  
  
./b2 --prefix=./bld --stagedir=./bld/stage --build-dir=./bld/int -j16 --build-type=complete --layout=tagged runtime-debugging=off debug-symbols=on address-model=64 cxxflags="-stdlib=libc++ -std=c++11 -fPIC" linkflags="-stdlib=libc++ -std=c++11 -fPIC" --without-python --without-iostreams boost.locale.icu=off boost.locale.iconv=on install > ./build.log

---

## Comment 1

> Username: lazar-ivanov  
> Created at: 2021-07-18 13:39:06 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-882058029  

Forgot to note that the build does find iconv (below):  
  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : arm  
    - C++11 mutex              : yes  
    - lockfree boost::atomic_flag : yes  
    - has stat::st_mtim        : no  
    - has stat::st_mtimensec   : no  
    - has stat::st_mtimespec   : yes  
    - has stat::st_birthtim    : no  
    - has stat::st_birthtimensec : no  
    - has stat::st_birthtimespec : yes  
    - has statx                : no  
    - has statx syscall        : no  
    - cxx11_auto_declarations  : yes  
    - cxx11_constexpr          : yes  
    - cxx11_defaulted_functions : yes  
    - cxx11_final              : yes  
    - cxx11_hdr_mutex          : yes  
    - cxx11_hdr_tuple          : yes  
    - cxx11_lambdas            : yes  
    - cxx11_noexcept           : yes  
    - cxx11_nullptr            : yes  
    - cxx11_rvalue_references  : yes  
    - cxx11_template_aliases   : yes  
    - cxx11_thread_local       : yes  
    - cxx11_variadic_templates : yes  
    - has_icu builds           : no  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam.  
note: to suppress this message, pass "--without-graph_parallel" to bjam.  
    - cxx11_alignas            : yes  
    - cxx11_decltype           : yes  
    - iconv (libc)             : no  
    - iconv (separate)         : yes  
    - native atomic int32 supported : yes  
    - native syslog supported  : yes  
    - pthread supports robust mutexes : no  
    - gcc visibility           : yes  
    - long double support      : yes  
warning: skipping optional Message Passing Interface (MPI) library.  
note: to enable MPI support, add "using mpi ;" to user-config.jam.  
note: to suppress this message, pass "--without-mpi" to bjam.  
note: otherwise, you can safely ignore this message.  
    - cxx11_static_assert      : yes  
    - std::fstream is moveable and swappable : yes  
    - Has Large File Support   : yes  
    - libbacktrace builds      : no  
    - addr2line builds         : yes  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : no

---

## Comment 2

> Username: Flamefire  
> Created at: 2021-07-19 07:19:56 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-882306456  

Cannot reproduce, must be something on your end.  
BTW: Instead of your cxxflags I suggest to use `cxxstd=11 stdlib=libc++`, maybe that is enough to solve your issue or point to the real problem (e.g. no static libc++?)

---

## Comment 3

> Username: lazar-ivanov  
> Created at: 2021-07-20 23:35:46 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-883773430  

Can you elaborate on what do you mean that you can't reproduce? Have you tried the b2 command above? Are you suggesting it is because of the cxxflags parameters? This happens for me on any of the Macs I have with 1.76 and not happening with 1.75.  
  
If you simply bootstrap.sh followed by the b2 command above should be easily reproducible.

---

## Comment 4

> Username: lazar-ivanov  
> Created at: 2021-07-20 23:37:28 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-883773984  

I also tried this on brand new Mac and it happens here too. The only thing I have installed on the Mac was Xcode + the command line tools which is of course needed to bring the toolchain.

---

## Comment 5

> Username: Flamefire  
> Created at: 2021-07-21 07:12:34 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-883949692  

> Can you elaborate on what do you mean that you can't reproduce?   
  
Doesn't happen for me on Linux with the command provided  
  
> Are you suggesting it is because of the cxxflags parameters?  
  
Only guessing here but yes and especially it may "point to the real problem (e.g. no static libc++?)"  
  
>  I have with 1.76 and not happening with 1.75.  
  
Is Boost.Locale the only library affected? Because it is literally the same for those 2 versions.  
  
Can you try again with 1.76 and attach the build log? It should rather look like `- cxx11_auto_declarations  : no [2]` (i.e. have those numbers for the different configurations)

---

## Comment 6

> Username: lazar-ivanov  
> Created at: 2021-07-23 01:54:50 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-885347487  

It is entirely possible that it doesn't happen on Linux. It is a different platform and different environment. I have not tried building it for Linux (yet), but I'm able to reproduce it on macOS 100% consistently and also with different macOS versions and clang toolchains. E.g. I can reproduce it on somewhat older intel Mac with macOS Mojave and Clang 10.0.0 and very recent arm Mac (m1) with macOS Big Sur and clang 12.0.5.  
  
I can try running with the parameters you specify and will let you know, but the command line I'm using should be legit and it should work fine.   
  
>> Is Boost.Locale the only library affected - no, but only one other library is affected - one of the libraries created by Boost.Stacktrace also has the same problem, but all other libraries are ok.  
  
I will attach the build.log file in my next comment in a couple of days.

---

## Comment 7

> Username: lazar-ivanov  
> Created at: 2021-07-25 21:25:22 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-886259866  

I did check on Linux and this problem does not happen there, but as I mentioned above this is not surprising as Linux is quite a different env than macOS (including e.g. iconv is part of libc there unlike on the Mac). I also did try with the  cxxstd=11 stdlib=libc++ parameters and this did not help. The - cxx11_auto_declarations in the log file is "yes". Attached are the two log files - one with the original b2 command line and the second with the cxxstd=11 stdlib=libc++ parameters (replacing the respective cxxflags and linkflags parameters).  
[build-log-with-cxxstd-stdlib-parameters.log](https://github.com/boostorg/locale/files/6874581/build-log-with-cxxstd-stdlib-parameters.log)  
[build-log-with-original-parameters.log](https://github.com/boostorg/locale/files/6874582/build-log-with-original-parameters.log)

---

## Comment 8

> Username: Flamefire  
> Created at: 2021-07-26 09:51:42 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-886551613  

```  
    - iconv (libc)             : no [10]  
    - iconv (separate)         : no [10]  
- Boost.Locale needs either iconv or ICU library to be built.  
    - iconv (libc)             : no [11]  
    - iconv (separate)         : no [11]  
- Boost.Locale needs either iconv or ICU library to be built.  
```  
  
That is a good hint. Can you create another log with: `./b2 --build-dir=./bld/int  --with-locale --layout=tagged runtime-debugging=off debug-symbols=on address-model=64 cxxstd=11 stdlib=libc++ runtime-link=static -d+2 --reconfigure -a --debug-building`?

---

## Comment 9

> Username: lazar-ivanov  
> Created at: 2021-07-26 15:41:48 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-886813830  

Attached is the log with the command line above  
[build-log-with-debug-parameters.log](https://github.com/boostorg/locale/files/6879486/build-log-with-debug-parameters.log)

---

## Comment 10

> Username: Flamefire  
> Created at: 2021-07-28 12:25:24 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-888266706  

Sorry, digging in the dark here and I'm not to familiar with b2 myself so need to ask you for some more info.  
Can you rerun the above command with `runtime-link=shared` instead of the static and for **both** attach also the `config.log` from `bld/int/boost/bin.v2/`?

---

## Comment 11

> Username: lazar-ivanov  
> Created at: 2021-08-01 12:00:21 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-890506668  

No worries and apologies for the delayed replies. Thank you for investigating it. See attached files (both build and config logs for the static and shared).  
[build-log-with-debug-parameters-shared.log](https://github.com/boostorg/locale/files/6912640/build-log-with-debug-parameters-shared.log)  
[config-log-with-debug-parameters-shared.log](https://github.com/boostorg/locale/files/6912641/config-log-with-debug-parameters-shared.log)  
[config-log-with-debug-parameters-static.log](https://github.com/boostorg/locale/files/6912642/config-log-with-debug-parameters-static.log)  
[build-log-with-debug-parameters-static.log](https://github.com/boostorg/locale/files/6912643/build-log-with-debug-parameters-static.log)

---

## Comment 12

> Username: Flamefire  
> Created at: 2021-08-02 08:28:01 UTC  
> Updated at: 2021-08-02 08:28:26 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-890834480  

Ok checked that and got (shortened) the following as the difference:  
  
```  
"clang++" -o "has_iconv" "has_iconv_libc_obj.o" -std=c++11 -g -fvisibility=hidden -fvisibility-inlines-hidden -static -stdlib=libc++  
  
"clang++" -o "has_iconv" "has_iconv_libc_obj.o" -std=c++11 -g -fvisibility=hidden -fvisibility-inlines-hidden -stdlib=libc++  
```  
  
As you can see the invocation from B2 is fully correct as far as I can tell.  
  
The error from the static build is: `ld: library not found for -lcrt0.o` which according to https://stackoverflow.com/questions/3801011/ld-library-not-found-for-lcrt0-o-on-osx-10-6-with-gcc-clang-static-flag hints that fully static builds on OSX are plain unsupported in most scenarios.  
  
--> We can't do much, maybe you can install/change something on your system so this works.  
  
As you said it has seemingly worked in 1.75, would you mind creating the above logs for that Boost version and on the same system too? I suspect either that it hasn't actually worked or something on your system has changed.

---

## Comment 13

> Username: lazar-ivanov  
> Created at: 2021-08-03 00:20:30 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-891416989  

I'm not sure what do you mean by "fully static builds", usually what it means is to link statically to the C++ standard library (libc++ or libstdc++), but not to link statically to libc or other OS level libraries. I believe this is also what this boost option means (runtume-link -- a.k.a. the c++ std lib runtime). I'm not sure why it fails to find this file and why the -static parameter works this way, but it clearly something with Boost.Locale because all other boost libraries (except one) are building and producing static libraries just fine. Also it builds and produces static libraries for 1.75, so it is broken only for Boost.Locale and only for 1.76 and onwards. Let me know if you want me to build some other library to diff and try to see how they are linking. Attached are the build and config files for 1.75 that you requested (for both static and shared command line).  
[1.75-build-log-with-debug-parameters-shared.log](https://github.com/boostorg/locale/files/6920216/1.75-build-log-with-debug-parameters-shared.log)  
[1.75-config-log-with-debug-parameters-shared.log](https://github.com/boostorg/locale/files/6920217/1.75-config-log-with-debug-parameters-shared.log)  
[1.75-build-log-with-debug-parameters-static.log](https://github.com/boostorg/locale/files/6920218/1.75-build-log-with-debug-parameters-static.log)  
[1.75-config-log-with-debug-parameters-static.log](https://github.com/boostorg/locale/files/6920219/1.75-config-log-with-debug-parameters-static.log)

---

## Comment 14

> Username: lazar-ivanov  
> Created at: 2021-08-03 00:21:25 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-891417355  

BTW, I will be traveling in the next week or so (in case I'm not able to respond), just fyi-

---

## Comment 15

> Username: Flamefire  
> Created at: 2021-08-03 07:25:51 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-891604965  

Just to be sure: Have you used the exact same b2 cmdline for the 1.75 build?  
  
The configure checks:  
in 1.75:  
`"clang++"   -o ".../has_iconv" ".../has_iconv_libc_obj.o"        -fPIC -std=c++11 -g -fvisibility=hidden -fvisibility-inlines-hidden`  
  
In 1.76:  
`"clang++"   -o ".../has_iconv" ".../has_iconv_libc_obj.o"        -std=c++11 -g -fvisibility=hidden -fvisibility-inlines-hidden -static -stdlib=libc++`  
  
I.e. missing the -fPIC and seemingly the -static.  
I'll check back with the b2 author but it is possible, that there is a bug newly introduced or fixed, so not sure what is "correct"

---

## Comment 16

> Username: lazar-ivanov  
> Created at: 2021-08-03 09:49:41 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-891703769  

Yes, I have used the same exact command lines for the 1.75 logs I sent above (see command lines below). I'm not sure why 1.76 is adding -static instead of -fPIC.  
  
./b2 --build-dir=./bld/int --with-locale --layout=tagged runtime-debugging=off debug-symbols=on address-model=64 cxxstd=11 stdlib=libc++ runtime-link=static -d+2 --reconfigure -a --debug-building > ./build.log  
./b2 --build-dir=./bld/int --with-locale --layout=tagged runtime-debugging=off debug-symbols=on address-model=64 cxxstd=11 stdlib=libc++ runtime-link=shared -d+2 --reconfigure -a --debug-building > ./build.log

---

## Comment 17

> Username: Flamefire  
> Created at: 2021-08-03 13:36:43 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-891853416  

Ok, the `-static` flag added to the configure check is due to a bugfix in recent B2 and should have been there before.  
Question is why it did work before because I'd expect that linking the library would fail  
  
Can you try `./b2 --build-dir=./bld/int --with-locale --layout=tagged runtime-debugging=off debug-symbols=on address-model=64 cxxstd=11 stdlib=libc++ runtime-link=static variant=shared -d+2 --reconfigure -a --debug-building > ./build.log` on 1.75 please?  
It should contain the commands used to compile and link the lib. I suspect it is missing the `-static` flag too. Or something else...

---

## Comment 18

> Username: lazar-ivanov  
> Created at: 2021-09-05 18:03:41 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-913199619  

On 1.75 this command line has failed with an error saying that variant=shared is invalid (see below & attached file, config file is empty):  
  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/build/feature.jam:491: in feature.validate-value-string from module feature  
error: "shared" is not a known value of feature <variant>  
error: legal values: "debug" "release" "profile" "debug-python"  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/build/property.jam:333: in validate1 from module property  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/build/property.jam:359: in property.validate from module property  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/build/build-request.jam:271: in convert-command-line-element from module build-request  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/build/build-request.jam:222: in build-request.convert-command-line-elements from module build-request  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/build-system.jam:774: in load from module build-system  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/Users/lazar/swblocks/dist-devenv5-darwin-20-arm/scratch/boost/boost_1_75_0/boost-build.jam:17: in module scope from module  
  
[1.75-build-log-with-debug-parameters-static-2.log](https://github.com/boostorg/locale/files/7112106/1.75-build-log-with-debug-parameters-static-2.log)

---

## Comment 19

> Username: Flamefire  
> Created at: 2021-09-14 14:08:58 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-919188281  

Oh, my bad: It should be `link=shared`: `./b2 --build-dir=./bld/int --with-locale --layout=tagged runtime-debugging=off debug-symbols=on address-model=64 cxxstd=11 stdlib=libc++ runtime-link=static link=shared -d+2 --reconfigure -a --debug-building > ./build.log`

---

## Comment 20

> Username: lazar-ivanov  
> Created at: 2024-01-29 18:27:06 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-1915320402  

Is someone interested to look into this issue? This is still happening with boost 1.84 on macOS Ventura and it is very frustrating because effectively you can't use the boost locale library if you are linking statically.

---

## Comment 21

> Username: lazar-ivanov  
> Created at: 2024-01-29 18:35:10 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-1915332783  

<img width="474" alt="Screenshot 2024-01-29 at 1 33 17 PM" src="https://github.com/boostorg/locale/assets/25238363/34ca5e11-8a94-453a-ab9e-e903fccc7860">  
  
The folder under runtime-link-static is basically empty - see attached screenshot

---

## Comment 22

> Username: Flamefire  
> Created at: 2024-02-03 18:16:03 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-1925419758  

I'd need the build logs each for 1.75 and newer created by using `./b2 --build-dir=./bld/int --with-locale --layout=tagged runtime-debugging=off debug-symbols=on address-model=64 cxxstd=11 stdlib=libc++ runtime-link=static link=shared -d+2 --reconfigure -a --debug-building > ./build.log` to investigate why it fails on your system.

---

## Comment 23

> Username: Flamefire  
> Created at: 2025-01-12 16:41:09 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-2585819625  

There were some releases since this issue so assuming this is fixed. Please comment or open a new issue if there are still problems.

---

## Comment 24

> Username: sanderstoks  
> Created at: 2026-01-04 16:43:32 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-3708237864  

Just reviving this again because I'm trying to build 1.89 on macOS and ran into the same issue.  I also get the "Boost.Locale needs either iconv or ICU library to be built." during a b2 run with "./b2 runtime-link=static link=static cflags=-fPIC cxxflags=-fPIC --prefix=/opt/Libraries/boost/boost_1_89_0 --user-config=./user-config.jam --with-locale".  If I don't specify the runtime-link=static, I get a libboost_locale.a

---

## Comment 25

> Username: sanderstoks  
> Created at: 2026-01-04 17:03:22 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-3708252958  

Extra info: After reading here: https://metashapes.com/blog/building-boost-locale-ios/, I tried adding the path to libiconv manually (after installing it via homebrew).  Unfortunately, it then complains with "ld: library 'crt0.o' not found", which according to here: https://stackoverflow.com/questions/3801011/ld-library-not-found-for-lcrt0-o-on-osx-10-6-with-gcc-clang-static-flag means that it's simply not supported to statically link to the runtime on Mac: "This option will not work on Mac OS X unless all libraries (including libgcc.a) have also been compiled with -static. Since neither a static version of libSystem.dylib nor crt0.o are provided, this option is not useful to most people."  
  
I think I'll have to give up on building boost with link-runtime=static.  Strange that only Boost.locale gave me this problem.

---

## Comment 26

> Username: Flamefire  
> Created at: 2026-01-04 17:06:59 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-3708255849  

>  I also get the "Boost.Locale needs either iconv or ICU library to be built."  
  
So that's the cause for the missing libs.  
  
So `link=shared` with any `runtime-link` works, while `link=static` only works without `runtime-link=static`, correct?     
Since which version is that the case for you? Is it also that 1.75 works and 1.76 does not?  
  
Please provide the `config.log` and `build.log` for `./b2 --build-dir=./bld/int --with-locale cflags=-fPIC cxxflags=-fPIC runtime-link=static link=static -d+2 --reconfigure -a --debug-building > ./build.log` for at least 1.89 and ideally for 1.75 or the latest version that works, if any.

---

## Comment 27

> Username: sanderstoks  
> Created at: 2026-01-04 17:18:37 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-3708264648  

I did not try any older versions, sorry.  I was trying to build 1.89, and stumbled upon this thread while trying to find a solution.

---

## Comment 28

> Username: templateK  
> Created at: 2026-01-26 21:40:31 UTC  
> Url: https://github.com/boostorg/locale/issues/66#issuecomment-3801889084  

@sanderstoks   
  
I've successfully built the boost19 with `link=shared,static`on macOS sonoma with custom version of iconv which is compiled dynamically and statically.  
  
`iconv` related`b2` options: `-sICONV_INCLUDE="/path/to/iconv/include"`   `-sICONV_LIBPATH="/path/to/iconv/lib"`
