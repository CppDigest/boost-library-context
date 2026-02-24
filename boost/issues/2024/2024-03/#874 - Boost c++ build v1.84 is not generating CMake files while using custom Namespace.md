# #874 - Boost c++ build v1.84 is not generating CMake files while using custom Namespace [Open]

> Username: kesavan66s  
> Created at: 2024-03-26 13:29:14 UTC  
> Updated at: 2024-03-26 13:29:14 UTC  
> Url: https://github.com/boostorg/boost/issues/874  

Boost c++ build v1.84 (using conan recipe) is not generating CMake files for all the components while using custom Namespace('boosts1d'). I am getting CMake files only for 'boost' and 'boost_headers'. Whereas in the same build system, if I am using default namespace 'boost', it is generating CMake file for all components.  
Build environment: Windows 11, Visual Studio 2019 (14.2)  
I am using 'b2' for this build which was generated using <Boost src>/bootstrap.bat  
I am using tools/bcp to apply the namespace and my command is as follows:  
<boost src>\dist\bin\bcp --namespace=boosts1d --namespace-alias --boost=<boost src> <Libraries> custom-boost  
Boost Build command:  
<boost src>\b2 -q target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=system --user-config=<boost src>\tools\build\user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=1 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.icu=on boost.locale.iconv=off --disable-iconv runtime-link=shared runtime-debugging=off threading=multi visibility=hidden link=shared variant=release --with-atomic --with-chrono --with-container --with-context --with-contract --with-coroutine --with-date_time --with-exception --with-filesystem --with-graph --with-iostreams --with-json --with-locale --with-log --with-math --with-nowide --with-program_options --with-random --with-regex --with-serialization --with-stacktrace --with-system --with-test --with-thread --with-timer --with-type_erasure --with-url --with-wave toolset=msvc cxxstd=17 pch=on -sICU_PATH=<.conan2>\p\icu41574ce3bbbab\p linkflags="advapi32.lib" cxxflags="-fPIC" install --prefix=<.conan2>\p\b\boostc608aa6df2787\p -j12 --abbreviate-paths -d0 --debug-configuration --build-dir="<.conan2>\p\b\boostc608aa6df2787\b\build-release"  
  
Is Boost build chain supports custom namespace?
