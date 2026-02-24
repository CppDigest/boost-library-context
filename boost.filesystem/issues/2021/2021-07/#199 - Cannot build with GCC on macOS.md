# #199 - Cannot build with GCC on macOS [Closed]

> Username: eschnett  
> Created at: 2021-07-14 14:33:15 UTC  
> Updated at: 2021-07-14 15:21:24 UTC  
> Closed at: 2021-07-14 15:20:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/199  

I want to build Boost with GCC on macOS, using Spack. This fails with errors such as  
```  
gcc.compile.c++ bin.v2/libs/filesystem/build/gcc-11/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/path.o  
libs/filesystem/src/path.cpp:1185:27: error: 'init_priority' attribute is not supported on this platform  
 1185 | const path_locale_deleter g_path_locale_deleter = {};  
      |                           ^~~~~~~~~~~~~~~~~~~~~  
libs/filesystem/src/path.cpp:1187:58: error: 'init_priority' attribute is not supported on this platform  
 1187 | const boost::filesystem::path g_dot_path(dot_path_literal);  
      |                                                          ^  
libs/filesystem/src/path.cpp:1189:66: error: 'init_priority' attribute is not supported on this platform  
 1189 | const boost::filesystem::path g_dot_dot_path(dot_dot_path_literal);  
      |                                                                  ^  
```  
  
The complete build output, including this error, is [spack-build-out.txt](https://github.com/boostorg/filesystem/files/6816790/spack-build-out.txt).  
  
I am building on mac OS 11.4 (Darwin 20.5.0) with GCC 11.1.0 (installed via Spack). I configured the `develop` branch of Boost with the options `cxxstd=17 +context +mpi`, which translates to `'./bootstrap.sh' '--prefix=/Users/eschnett/src/CarpetX/spack/opt/spack/darwin-bigsur-skylake/gcc-11.1.0/boost-develop-edadff2hnbldeqf66qcqam66ziao7p37' '--with-toolset=gcc' '--with-libraries=test,random,mpi,thread,date_time,system,regex,timer,chrono,filesystem,log,graph,iostreams,program_options,math,atomic,locale,wave,context,serialization,exception,graph_parallel' '--without-icu'` (see the top of the build output).  
  
This might be related to how `boost::filesystem` detects whether `init_priority` is available. I see that `boost::nowide` uses special code in a `CMakeLists.txt` file to detect whether this feature is available, whereas `boost::filesystem` only checks the GCC version number.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-07-14 15:21:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/199#issuecomment-879984722  

Thanks for the report.
