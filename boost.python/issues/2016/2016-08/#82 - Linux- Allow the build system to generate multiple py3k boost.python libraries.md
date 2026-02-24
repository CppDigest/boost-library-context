# #82 - Linux: Allow the build system to generate multiple py3k boost.python libraries [Closed]

> Username: yan12125  
> Created at: 2016-08-10 12:11:24 UTC  
> Updated at: 2018-04-09 01:23:38 UTC  
> Closed at: 2018-04-09 01:23:38 UTC  
> Url: https://github.com/boostorg/python/issues/82  

Since 89100353dbcd9c5f6171d66931161a07becec597 ([SVN r56305](http://ci.boost.org/svn-trac/changeset/56305/trunk), [GSoC 2009](https://www.google-melange.com/archive/gsoc/2009/orgs/boost/projects/bhy.html)), the Python 3 variant of Boost.Python is built as `libboost_python3.so` (on Linux), and since 37b45d2baf5188f7be7e2883eff723760bf43e14 ([SVN r59987](http://ci.boost.org/svn-trac/changeset/59987/trunk), [trac 3544](https://svn.boost.org/trac/boost/ticket/3544)), users can use `--python-buildid` to add additional strings in the library name. (Though it's broken since 1.48, see [trac 6286](https://svn.boost.org/trac/boost/ticket/6286))  
  
Based on these options, Linux distributions use this option to build multiple Boost.Python libraries against different Python versions. For example Debian's `rules`([Debian source tarball](http://http.debian.net/debian/pool/main/b/boost1.61/boost1.61_1.61.0+dfsg-2.1.debian.tar.xz)):  
  
``` bash  
    for pyver in $(pyversions); do \  
        pyid=$$(echo $$pyver | tr -d .); \  
        echo "Building Boost.Python for python version $$pyver"; \  
        $(JAM) --with-python --with-mpi --python-buildid=py$$pyid python=$$pyver; \  
        mv stage/lib/mpi.so stage/lib/mpi-py$$pyid.so || true; \  
    done  
```  
  
So on Debian those files are `libboost_python-py35.so` and `libboost_python-py27.so` ([amd64 filelist](https://packages.debian.org/sid/amd64/libboost-python1.61-dev/filelist)). On the other way, Gentoo have a different scheme:  
  
``` bash  
PYTHON_OPTIONS=" --python-buildid=${EPYTHON#python}"  
```  
  
So on Gentoo files are `libboost_python-3.5.so` and `libboost_python-2.7.so`. Some other distributions, like Fedora ([build spec](https://apps.fedoraproject.org/packages/boost-python3/sources/spec/), [file list](https://apps.fedoraproject.org/packages/boost-python3/contents/) and Arch Linux ([build script](https://git.archlinux.org/svntogit/packages.git/tree/trunk/PKGBUILD?h=packages/boost), [file list](https://www.archlinux.org/packages/extra/x86_64/boost-libs/files/)), does not use `--python-buildid`, so they have `libboost_python3.so` and `libboost_python.so`.  
  
With such a case, determine a portable way to link to the correct Boost.Python library is very difficult, especially for projects using CMake. ([proposed solution at CMake](https://gitlab.kitware.com/cmake/cmake/merge_requests/35))  
  
Here I suggest Boost.Python to have a unified suffix scheme for libraries targetting different Python version, so that downstream users can follow it. An idea is use the same name libpythonXX.so. For example, `libboost_python-3.5m.so` targets `libpython3.5m.so`, and `libboost_python-2.7.so` targets `libpython2.7.so`. The suffix can be detected with the following Python script:  
  
``` python  
'.'.join(map(str, sys.version_info[:2])) + (sys.abiflags if hasattr(sys, 'abiflags') else '')  
```

---

## Comment 1

> Username: yan12125  
> Created at: 2016-08-13 10:10:39 UTC  
> Url: https://github.com/boostorg/python/issues/82#issuecomment-239613644  

Anybody here? I go to https://svn.boost.org/trac/boost/newticket and it tells me "USE GITHUB" when I choose the component Boost.Python, so I guess this is the correct place for reporting issues?

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-08-13 22:36:04 UTC  
> Url: https://github.com/boostorg/python/issues/82#issuecomment-239645073  

It is indeed.

---

## Comment 3

> Username: yan12125  
> Created at: 2016-08-29 11:02:53 UTC  
> Url: https://github.com/boostorg/python/issues/82#issuecomment-243094779  

I have posted a pull request to #83, while I'm not sure whether I need to patch Jamfile.v2 as well. Is Boost.Python moving from Boost.Build to SCons once building on Visual Studio is working?

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2016-08-29 12:53:20 UTC  
> Url: https://github.com/boostorg/python/issues/82#issuecomment-243115405  

Hello,  
thanks for working on this. Here are a few thoughts:  
- I'm aware of the various ways Boost is packaged for different platforms / distributions, and I agree this is largely Boost's own fault: we should provide the tools to generate (binary) distributions with consistent naming schemes, so end users don't have to worry about different names on different platforms.  
- SCons is a recent addition that I have been working on the make Boost.Python more autonomous / independent from the rest of Boost. While I encourage everyone to use it to build Boost.Python stand-alone, I doubt Boost itself will stop distributing Boost.Python as an integral part of Boost (or downstream package maintainers will use scons to generate Boost.Python packages). Rather, I expect the two to coexist for quite a while. Thus, it might be best to patch both and make sure they produce the same artifacts.  
-  All the above being said, I'm not quite sure yet what the right naming convention is that will be accepted by everyone and fit all critical use-cases. I have seen your pull request (which looks simple enough), but am still thinking about what we need.

---

## Comment 5

> Username: yan12125  
> Created at: 2016-08-29 14:21:36 UTC  
> Url: https://github.com/boostorg/python/issues/82#issuecomment-243138253  

Thanks for clarafication. Give me some time to study Boost.Build syntaxes...  
  
> Boost itself will stop distributing Boost.Python as an integral part of Boost (or downstream package maintainers will use scons to generate Boost.Python packages).  
  
This might not be something bad. Distributions can create packages like boost, boost-python35, boost-python27, etc. It reduces the burden to maintain multiple Boost.Python versions in a single package. Users/Application developers can also switch among Boost.Python versions easily. Currently on Gentoo, targetting new Python version of Boost.Python requires rebuilding the whole Boost.  
  
> I'm not quite sure yet what the right naming convention is that will be accepted by everyone and fit all critical use-case  
  
For me changing the naming scheme step by step is good - it's difficult to have a perfect solution at the first time. I guess it's OK to change the library name between Boost releases (for example 1.61 and 1.62). Applications using Boost.Python need to rebuild in each Boost release, isn't it? After all, adding the Python ABI signature is the first step to universal Boost.Python naming scheme.

---

## Comment 6

> Username: archenroot  
> Created at: 2017-06-12 13:27:37 UTC  
> Url: https://github.com/boostorg/python/issues/82#issuecomment-307788826  

Any update on this, or some quick hack via some variable to be passed to CMake system, so it won't fail like this?  
  
```  
-- Found PythonLibs: /usr/lib64/libpython3.5m.so (found version "3.5.3")   
CMake Warning at /usr/share/cmake/Modules/FindBoost.cmake:1527 (message):  
  No header defined for python3; skipping header check  
Call Stack (most recent call first):  
  CMakeLists.txt:11 (find_package)  
  
  
CMake Error at /usr/share/cmake/Modules/FindBoost.cmake:1853 (message):  
  Unable to find the requested Boost libraries.  
  
  Boost version: 1.62.0  
  
  Boost include path: /usr/include  
  
  Could not find the following Boost libraries:  
  
          boost_python3  
  
  No Boost libraries were found.  You may need to set BOOST_LIBRARYDIR to the  
  directory containing Boost libraries or BOOST_ROOT to the location of  
  Boost.  
Call Stack (most recent call first):  
  CMakeLists.txt:11 (find_package)  
  
  
-- Found PythonLibs: /usr/lib64/libpython3.5m.so (found suitable version "3.5.3", minimum required is "3.5")   
-- <<< Gentoo configuration >>>  
  
```  
I am on Gentoo system and boost generates following python libs:  
```  
/usr/lib64/libboost_python-2.7-mt.so  
/usr/lib64/libboost_python-2.7.so  
/usr/lib64/libboost_python-2.7.so.1.62.0  
/usr/lib64/libboost_python-3.5-mt.so  
/usr/lib64/libboost_python-3.5.so  
/usr/lib64/libboost_python-3.5.so.1.62.0  
  
```

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2018-02-16 01:00:55 UTC  
> Url: https://github.com/boostorg/python/issues/82#issuecomment-366114309  

As mentioned in https://github.com/boostorg/python/issues/185, I have (tentatively) addressed this issue by adding a Python version suffix to the library. For Python 2.7 the library name would be `boost_python27`.  
This is committed as https://github.com/boostorg/python/commit/d4d41d94aecc9f8098aabd3587fcb95458451f71 to the `develop` branch, and will be merged into master in time for the next release, unless someone objects and convinces me of a better naming scheme.
