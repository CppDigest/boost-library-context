# #758 - Cygwin build: b2 not working [Open]

> Username: adritium  
> Created at: 2023-03-26 13:26:33 UTC  
> Updated at: 2023-03-26 13:49:16 UTC  
> Url: https://github.com/boostorg/boost/issues/758  

These are my commands; `b2` exist but doesn't do anything.  
Same thing happens when I checkout tag `1.81.0`  
  
```  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ git pull  
Already up to date.  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ git submodule update --init --recursive  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ git status -vv  
On branch master  
Your branch is up to date with 'origin/master'.  
  
nothing to commit, working tree clean  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ git clean -fdx  
Removing b2.exe  
Removing project-config.jam  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ git reset --hard  
HEAD is now at 27c16dbb69 Update multiprecision from master  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ git clean -fdx  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ ./bootstrap.sh  
Building B2 engine..  
  
###  
###  
### Using 'gcc' toolset.  
###  
###  
  
g++ (GCC) 11.3.0  
Copyright (C) 2021 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
  
###  
###  
  
> g++ -x c++ -std=c++11 -D_GNU_SOURCE -O2 -s -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp execunix.cpp filesys.cpp filent.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp -o b2  
tools/build/src/engine/b2  
Detecting Python version... 3.9  
Detecting Python root... /usr  
Unicode/ICU support for Boost.Regex?... /usr  
Generating B2 configuration in project-config.jam for gcc...  
  
Bootstrapping is done. To build, run:  
  
    ./b2  
  
To generate header files, run:  
  
    ./b2 headers  
  
The configuration generated uses gcc to build by default. If that is  
unintended either use the --with-toolset option or adjust configuration, by  
editing 'project-config.jam'.  
  
Further information:  
  
   - Command line help:  
     ./b2 --help  
  
   - Getting started guide:  
     http://www.boost.org/more/getting_started/unix-variants.html  
  
   - B2 documentation:  
     http://www.boost.org/build/  
  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ ./b2 --help  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ ls -la b2  
-rwxrwxr-x+ 1 u114758 Domain Users 240128 Mar 26 09:21 b2  
  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ where b2  
C:\work\misc\boost\boost-repo\boost\b2.exe  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$ du -h b2  
236K    b2  
  
user /cygdrive/c/work/misc/boost/boost-repo/boost  
$  
  
  
```

---

## Comment 1

> Username: adritium  
> Created at: 2023-03-26 13:49:15 UTC  
> Url: https://github.com/boostorg/boost/issues/758#issuecomment-1484102528  

I just ran `bootstrap.sh` under MSYS2 and it works:  
  
```  
user MSYS /c/work/misc/boost/boost-repo  
$ ./b2 --help  
B2 4.9-git  
  
Project-specific help:  
  
  Project has jamfile at Jamroot  
  
Usage:  
  
  b2 [options] [properties] [install|stage]  
  
  Builds and installs Boost.  
  
Targets and Related Options:  
  
  install                 Install headers and compiled library files to the  
  =======                 configured locations (below).  
  
  --prefix=<PREFIX>       Install architecture independent files here.  
                          Default: C:\Boost on Windows  
                          Default: /usr/local on Unix, Linux, etc.  
  
  --exec-prefix=<EPREFIX> Install architecture dependent files here.  
                          Default: <PREFIX>  
  
  --libdir=<LIBDIR>       Install library files here.  
                          Default: <EPREFIX>/lib  
  
  --includedir=<HDRDIR>   Install header files here.  
                          Default: <PREFIX>/include  
  
  --cmakedir=<CMAKEDIR>   Install CMake configuration files here.  
                          Default: <LIBDIR>/cmake  
  
  --no-cmake-config       Do not install CMake configuration files.  
  
  stage                   Build and install only compiled library files to the  
  =====                   stage directory.  
  
  --stagedir=<STAGEDIR>   Install library files here  
                          Default: ./stage  
  
Other Options:  
  
  --build-type=<type>     Build the specified pre-defined set of variations of  
                          the libraries. Note, that which variants get built  
                          depends on what each library supports.  
  
                              -- minimal -- (default) Builds a minimal set of  
                              variants. On Windows, these are static  
                              multithreaded libraries in debug and release  
                              modes, using shared runtime. On Linux, these are  
                              static and shared multithreaded libraries in  
                              release mode.  
  
                              -- complete -- Build all possible variations.  
  
  --build-dir=DIR         Build in this location instead of building within  
                          the distribution tree. Recommended!  
  
  --show-libraries        Display the list of Boost libraries that require  
                          build and installation steps, and then exit.  
  
  --layout=<layout>       Determine whether to choose library names and header  
                          locations such that multiple versions of Boost or  
                          multiple compilers can be used on the same system.  
  
                              -- versioned -- Names of boost binaries include  
                              the Boost version number, name and version of  
                              the compiler and encoded build properties. Boost  
                              headers are installed in a subdirectory of  
                              <HDRDIR> whose name contains the Boost version  
                              number.  
  
                              -- tagged -- Names of boost binaries include the  
                              encoded build properties such as variant and  
                              threading, but do not including compiler name  
                              and version, or Boost version. This option is  
                              useful if you build several variants of Boost,  
                              using the same compiler.  
  
                              -- system -- Binaries names do not include the  
                              Boost version number or the name and version  
                              number of the compiler. Boost headers are  
                              installed directly into <HDRDIR>. This option is  
                              intended for system integrators building  
                              distribution packages.  
  
                          The default value is 'versioned' on Windows, and  
                          'system' on Unix.  
  
  --buildid=ID            Add the specified ID to the name of built libraries.  
                          The default is to not add anything.  
  
  --python-buildid=ID     Add the specified ID to the name of built libraries  
                          that depend on Python. The default is to not add  
                          anything. This ID is added in addition to --buildid.  
  
  --help                  This message.  
  
  --with-<library>        Build and install the specified <library>. If this  
                          option is used, only libraries specified using this  
                          option will be built.  
  
  --without-<library>     Do not build, stage, or install the specified  
                          <library>. By default, all libraries are built.  
  
Properties:  
  
  toolset=toolset         Indicate the toolset to build with.  
  
  variant=debug|release   Select the build variant  
  
  link=static|shared      Whether to build static or shared libraries  
  
  threading=single|multi  Whether to build single or multithreaded binaries  
  
  runtime-link=static|shared  
                          Whether to link to static or shared C and C++  
                          runtime.  
  
  
General command line usage:  
  
    b2 [options] [properties] [targets]  
  
  Options, properties and targets can be specified in any order.  
  
Important Options:  
  
  * --clean Remove targets instead of building  
  * -a Rebuild everything  
  * -n Don't execute the commands, only print them  
  * -d+2 Show commands as they are executed  
  * -d0 Suppress all informational messages  
  * -q Stop at first error  
  * --reconfigure Rerun all configuration checks  
  * --debug-configuration Diagnose configuration  
  * --debug-building Report which targets are built with what properties  
  * --debug-generator Diagnose generator search/execution  
  
Further Help:  
  
  The following options can be used to obtain additional documentation.  
  
  * --help-options Print more obscure command line options.  
  * --help-internal B2 implementation details.  
  * --help-doc-options Implementation details doc formatting.  
  
...found 1 target...  
  
u114758@BIORAD27SVuLjUe MSYS /c/work/misc/boost/boost-repo  
$  
  
```
