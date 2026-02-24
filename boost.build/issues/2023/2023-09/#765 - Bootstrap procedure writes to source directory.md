# #765 - Bootstrap procedure writes to source directory [Closed]

> Username: iskunk  
> Created at: 2023-09-20 21:56:13 UTC  
> Updated at: 2023-09-24 19:34:03 UTC  
> Closed at: 2023-09-24 19:34:02 UTC  
> Url: https://github.com/boostorg/build/issues/765  

If the source tree (`/tmp/boost_1_83_0/` in this example) is read-only, and we operate from a separate (writable) build directory, then guessing the toolset fails as follows  
```  
skunk@darkstar:/tmp/boost-build$ /tmp/boost_1_83_0/tools/build/src/engine/build.sh --verbose --guess-toolset  
> g++ -x c++ -std=c++11  check_cxx11.cpp  
/usr/bin/ld: cannot open output file a.out: Permission denied  
collect2: error: ld returned 1 exit status  
> g++ -x c++ -std=c++11 -D_GNU_SOURCE  check_cxx11.cpp  
/usr/bin/ld: cannot open output file a.out: Permission denied  
collect2: error: ld returned 1 exit status  
> g++ -x c++ -std=c++11 -pthread  check_cxx11.cpp  
/usr/bin/ld: cannot open output file a.out: Permission denied  
collect2: error: ld returned 1 exit status  
> clang++ -x c++ -std=c++11  check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: clang++: not found  
> xlC_r   check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: xlC_r: not found  
> pgc++ -std=c++11  check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: pgc++: not found  
> pathCC   check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: pathCC: not found  
> como   check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: como: not found  
> bc++ -tC -q  check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: bc++: not found  
> aCC -AA  check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: aCC: not found  
> /opt/SUNWspro/bin/CC -std=c++11  check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: /opt/SUNWspro/bin/CC: not found  
> cxx   check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: cxx: not found  
> cpp   check_cxx11.cpp  
cc1plus: fatal error: opening output file check_cxx11.cpp: Permission denied  
compilation terminated.  
> CC   check_cxx11.cpp  
/tmp/boost_1_83_0/tools/build/src/engine/build.sh: 120: CC: not found  
  
Could not find a suitable toolset.  
  
You can specify the toolset as the argument, i.e.:  
    ./build.sh [options] gcc  
  
Toolsets supported by this script are:  
    acc, clang, como, gcc, intel-darwin, intel-linux, kcc, kylix, mipspro,  
    pathscale, pgi, qcc, sun, sunpro, tru64cxx, vacpp  
  
For any toolset you can override the path to the compiler with the '--cxx'  
option. You can also use additional flags for the compiler with the  
'--cxxflags' option.  
  
A special toolset; cxx, is available which is used as a fallback when a more  
specific toolset is not found and the cxx command is detected. The 'cxx'  
toolset will use the '--cxx' and '--cxxflags' options, if present.  
  
Options:  
    --help                  Show this help message.  
    --verbose               Show messages about what this script is doing.  
    --debug                 Build b2 with debug information, and no  
                            optimizations.  
    --guess-toolset         Print the toolset we can detect for building.  
    --cxx=CXX               The compiler exec to use instead of the detected  
                            compiler exec.  
    --cxxflags=CXXFLAGS     The compiler flags to use in addition to the  
                            flags for the detected compiler.  
```  
  
If I make the source tree writable, then the same command succeeds:  
```  
skunk@darkstar:/tmp/boost-build$ /tmp/boost_1_83_0/tools/build/src/engine/build.sh --verbose --guess-toolset  
> g++ -x c++ -std=c++11  check_cxx11.cpp  
gcc  
```  
  
The configuration and build procedure must not write to the source tree in any way. Not only could the source tree be mounted read-only in one of various ways (NFS, Docker volume mount, ISO image, etc.), source tree timestamps are often used to set `SOURCE_DATE_EPOCH` in reproducible builds and writing to the source tree wrecks that.  
  
Whatever scratch space is needed by these scripts should come from the build directory, probably inferred from the current working directory.

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2023-09-24 19:34:01 UTC  
> Url: https://github.com/boostorg/build/issues/765#issuecomment-1732651572  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
