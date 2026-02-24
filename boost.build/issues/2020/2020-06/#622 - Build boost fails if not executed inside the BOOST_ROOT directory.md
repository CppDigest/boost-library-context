# #622 - Build boost fails if not executed inside the BOOST_ROOT directory [Closed]

> Username: randre70  
> Created at: 2020-06-12 11:25:55 UTC  
> Updated at: 2025-01-13 14:46:45 UTC  
> Closed at: 2020-09-26 14:01:58 UTC  
> Url: https://github.com/boostorg/build/issues/622  

I fail to build boost from source outside the of the source directory as suggested on boost.org.  
  
Preparing directory structure:  
  
```bash  
$ mkdir build  
$ mkdir tmp  
$ cd tmp  
$ curl -L -o boost.tar.gz https://dl.bintray.com/boostorg/release/1.73.0/source/boost_1_73_0.tar.gz  
$ tar xzf boost.tar.gz  
$ mv boost_1_73_0 boost  
```  
  
Booststrap:  
  
```bash  
$ boost/bootstrap.sh \  
  --prefix=`pwd`/../build \  
  --exec-prefix=`pwd`/../build  
  
Building Boost.Build engine with toolset gcc... tools/build/src/engine/b2  
Detecting Python version... 3.8  
Detecting Python root... /usr  
Unicode/ICU support for Boost.Regex?... not found.  
Generating Boost.Build configuration in project-config.jam for gcc...  
  
Bootstrapping is done. To build, run:  
  
    ./b2  
      
  
```  
Executing b2:  
  
```bash  
$ ./b2  
  
Unable to load B2: could not find "boost-build.jam"  
---------------------------------------------------------------  
BOOST_ROOT must be set, either in the environment, or   
on the command-line with -sBOOST_ROOT=..., to the root  
of the boost installation.  
  
```  
  
Executing b2 with commandline-parameter:  
  
```bash  
$ ./b2 -sBOOST_ROOT=`pwd`/boost   
error: no Jamfile in current directory found, and no target references specified.  
  
```  
  
Copying boost-build.jam into current directory and running b2 again:  
  
```bash  
$ cp boost/boost-build.jam .  
$ ./b2 -sBOOST_ROOT=`pwd`/boost   
Unable to load B2: could not find build system.  
---------------------------------------------------------  
/home/me/Documents/tmp/boost-build.jam attempted to load the build system by invoking  
  
   'boost-build tools/build/src ;'  
  
but we were unable to find "bootstrap.jam" in the specified directory  
  
```  
  
Created empty Jamroot.jam (as suggested elsewhere, older documentation?)  
  
```bash  
$ touch Jamroot.jam  
$ ./b2 -sBOOST_ROOT=`pwd`/boost   
...found 1 target...  
  
```  
  
No further output. Nothing created.  
  
I tried to modify the boost-build.jam to no avail ...   
  
Please advise how to successfully build boost from outside the root directory.

---

## Comment 1

> Username: randre70  
> Created at: 2020-06-12 11:40:22 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-643225772  

BTW: how do you build if you checkout from github?

---

## Comment 2

> Username: randre70  
> Created at: 2020-06-12 11:47:02 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-643228266  

Deleted that Jamroot.jam file and tried ...  
  
```bash  
$ rm -f Jamroot.jam  
$ ./b2 -sBOOST_ROOT=`pwd`/boost --build-boost=boost/tools/build/src  
error: no Jamfile in current directory found, and no target references specified.  
  
```

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-07-13 09:28:53 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-657440954  

I'm having the same issue and found that it was introduced with the porting from Jam to C++: https://github.com/boostorg/build/commit/59b7a6dc69c3695581b38f4f1c643985895eeeaa  
  
Previously the code for locating the boost-build.jam was:  
  
```  
    local search-path = $(BOOST_BUILD_PATH) $(BOOST_ROOT) ;  
    local self = [ SELF_PATH ] ;  
    local boost-build-relative = ../../share/boost-build ;  
    local self-based-path = [ NORMALIZE_PATH $(boost-build-relative:R=$(self)) ] ;  
  
    local boost-build-files =  
        [ find-to-root [ PWD ] : boost-build.jam ]  
        [ GLOB $(self-based-path) : boost-build.jam ]  
        # Another temporary measure so Jam works with B2 v1.  
        [ GLOB $(search-path) : boost-build.jam ] ;  
```  
So judging from this it searched the directory b2 is located in and hence successfully found the boost-build.jam file in the boost root.  
  
The ported version does not do this. The only relative-to-b2 paths it checks are `"/../.b2/kernel/"` and `"/../../share/boost-build/"` but not in the same folder as b2: https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/engine/startup.cpp#L183-L201  
  
The error I then see is:  
```  
+/home/travis/boost-local/b2 -q --toolset=clang cxxflags=-std=c++17 -Wno-unused-local-typedefs -Wno-unused-function -Wno-deprecated-declarations --coverage linkflags=--coverage -j3  
Unable to load B2: could not find 'boost-build.jam'  
---------------------------------------------------  
Attempted search from '/home/travis/build/mat007/turtle/test' up to the root at '/home/travis/boost-local/b2'  
Please consult the documentation at 'https://boostorg.github.io/build/'.  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2020-08-24 09:54:54 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-679030441  

I see the same problem in the boostorg/unordered Appveyor: https://ci.appveyor.com/project/danieljames/unordered-qtwe6/builds/34816353/job/cii6m35qmi9g7p1r  
  
Can this please be fixed?

---

## Comment 5

> Username: pdimov  
> Created at: 2020-08-24 10:06:00 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-679035776  

Same problem on Travis: https://travis-ci.org/github/boostorg/unordered/jobs/720590517

---

## Comment 6

> Username: grafikrobot  
> Created at: 2020-09-26 14:07:16 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-699500044  

@Flamefire the issue is not that it doesn't search in the b2 location. It's that BOOST_ROOT is not searched.

---

## Comment 7

> Username: Flamefire  
> Created at: 2020-09-28 09:02:10 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-699878861  

Sure but my point still stands: Previously it searched the folder where `b2` is located, didn't it? At least that is how I read `[ GLOB $(self-based-path) : boost-build.jam ]`  
  
So was that an intended change? If so it should at least be announced.

---

## Comment 8

> Username: grafikrobot  
> Created at: 2020-09-28 12:36:51 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-699979228  

> Sure but my point still stands: Previously it searched the folder where `b2` is located, didn't it? At least that is how I read `[ GLOB $(self-based-path) : boost-build.jam ]`  
>   
> So was that an intended change? If so it should at least be announced.  
  
The old logic is:  
```  
# Search b2 path plus boost root  
    local search-path = $(BOOST_BUILD_PATH) $(BOOST_ROOT) ;  
# This is the path to b2 exec  
    local self = [ SELF_PATH ] ;  
# A relative path from install-prefix/bin/b2.exe to install-prefix/share/boost-build  
    local boost-build-relative = ../../share/boost-build ;  
# Full path to install-prefix/share/boost-build. The :R=(self) roots the path relative to the b2.exe dir.  
    local self-based-path = [ NORMALIZE_PATH $(boost-build-relative:R=$(self)) ] ;  
  
# Search possible boost-build.jam locations  
    local boost-build-files =  
# All dirs from current dir up to drive root.  
        [ find-to-root [ PWD ] : boost-build.jam ]  
# install-prefix/share/boost-build  
        [ GLOB $(self-based-path) : boost-build.jam ]  
# All dirs listed in BOOST_BUILD_PATH + BOOST_ROOT  
        # Another temporary measure so Jam works with B2 v1.  
        [ GLOB $(search-path) : boost-build.jam ] ;  
```  
Hence it never searched the b2.exe dir itself. Unless by coincidence in which the b2.exe is located in PWD. The new logic does the same. Except it searches for "install-prefix/share/boost-build/src/kernel/boost-build.jam" instead (after I also fixed that path). As I removed the "install-prefix/share/boost-build/boost-build.jam" file which was superfluous since all it did was to repoint to the src/kernel location.  
  
PS. The fact that the old Jam logic was easily misread and the new C++ logic was seemingly obvious makes me glad I ported this code.

---

## Comment 9

> Username: emmenlau  
> Created at: 2022-01-18 21:42:57 UTC  
> Updated at: 2022-01-19 08:39:46 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-1015860718  

I've been reading this whole thread about three times, and I think I'm still affected by this (or a very similar problem) in boost 1.78.0 (official archive download). Please advise if I should open a new issue.  
  
I'm trying to create an out of tree (source) build. Here are (slightly shortened) instructions of what I do, and what I get:  
```  
rm -fr /tmp/boost && \  
mkdir /tmp/boost && \  
/home/user/boost-1.78.0/booststrap.sh  
```  
The bootstrapping works and creates `b2` in the temporary build location. However it also creates copies of `b2` and `bjam` in `<boost sources>/tools/build/src/engine/`, which I think is not correct for an out of source build. For my use case, I would prefer a clean source tree, because my source folders are automatically synced via a cloud service, and the binary files should not transfer to other machines.  
  
Then I run:  
```  
./b2 -sBOOST_ROOT=/home/user/boost-1.78.0 install  
```  
which returns:  
```  
notice: found boost-build.jam at /home/user/boost-1.78.0/boost-build.jam  
notice: loading B2 from /home/user/boost-1.78.0/tools/build/src/bootstrap.jam  
notice: Searching '/etc' '/home/user' '/home/user/boost-1.78.0/tools/build/src' '/home/user/boost-1.78.0/tools/build/src/kernel' '/home/user/boost-1.78.0/tools/build/src/util' '/home/user/boost-1.78.0/tools/build/src/build' '/home/user/boost-1.78.0/tools/build/src/tools' '/home/user/boost-1.78.0/tools/build/src/contrib' '/home/user/boost-1.78.0/tools/build/src/.' for site-config configuration file 'site-config.jam'.  
notice: Configuration file 'site-config.jam' not found in '/etc' '/home/user' '/home/user/boost-1.78.0/tools/build/src' '/home/user/boost-1.78.0/tools/build/src/kernel' '/home/user/boost-1.78.0/tools/build/src/util' '/home/user/boost-1.78.0/tools/build/src/build' '/home/user/boost-1.78.0/tools/build/src/tools' '/home/user/boost-1.78.0/tools/build/src/contrib' '/home/user/boost-1.78.0/tools/build/src/.'.  
notice: Searching '/home/user' '/home/user/boost-1.78.0/tools/build/src' '/home/user/boost-1.78.0/tools/build/src/kernel' '/home/user/boost-1.78.0/tools/build/src/util' '/home/user/boost-1.78.0/tools/build/src/build' '/home/user/boost-1.78.0/tools/build/src/tools' '/home/user/boost-1.78.0/tools/build/src/contrib' '/home/user/boost-1.78.0/tools/build/src/.' for user-config configuration file 'user-config.jam'.  
notice: Configuration file 'user-config.jam' not found in '/home/user' '/home/user/boost-1.78.0/tools/build/src' '/home/user/boost-1.78.0/tools/build/src/kernel' '/home/user/boost-1.78.0/tools/build/src/util' '/home/user/boost-1.78.0/tools/build/src/build' '/home/user/boost-1.78.0/tools/build/src/tools' '/home/user/boost-1.78.0/tools/build/src/contrib' '/home/user/boost-1.78.0/tools/build/src/.'.  
notice: Searching '.' for project-config configuration file 'project-config.jam'.  
notice: Loading project-config configuration file 'project-config.jam' from '.'.  
notice: will use 'clang++' for clang-linux, condition <toolset>clang-linux-13  
notice: [python-cfg] Configuring python...  
notice: [python-cfg]   user-specified version: "3.8"  
notice: [python-cfg]   user-specified cmd-or-prefix: "/usr"  
notice: [python-cfg] Checking interpreter command "/usr/bin/python3.8"...  
notice: [python-cfg] running command '/usr/bin/python3.8 -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "/usr/bin/python3.8"  
notice: [python-cfg]   include path: "/usr/include/python3.8"  
notice: [python-cfg]   library path: "/usr/lib/python3.8/config" "/usr/lib"  
notice: [python-cfg] Checking for NumPy...  
notice: [python-cfg] running command '/usr/bin/python3.8 -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"'  
notice: [python-cfg] NumPy enabled  
error: no Jamfile in current directory found, and no target references specified.  
```  
The build works immediately if I build from inside the source tree. But this is not what I want. Am I doing something wrong?

---

## Comment 10

> Username: TheDreamsWind  
> Created at: 2023-07-27 07:47:45 UTC  
> Updated at: 2023-07-27 07:48:08 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-1653079529  

I don't understand why this issue is closed, since up to now the latest stable version 1.82.0 still has this problem. If boost is not supposed/designed to be built in a separate from the sources folder, it at least should be said explicitly

---

## Comment 11

> Username: emmenlau  
> Created at: 2025-01-13 14:46:43 UTC  
> Url: https://github.com/boostorg/build/issues/622#issuecomment-2587302600  

The issue persists all the way to boost 1.87.0. Its unclear why this issue is closed without comment or fix.
