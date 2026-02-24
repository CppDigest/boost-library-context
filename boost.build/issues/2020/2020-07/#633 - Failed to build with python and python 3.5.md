# #633 - Failed to build with python and python 3.5 [Open]

> Username: joergboe  
> Created at: 2020-07-06 12:30:53 UTC  
> Updated at: 2021-05-29 17:22:18 UTC  
> Url: https://github.com/boostorg/build/issues/633  

OS: RHEL 7  
Python 3.5.2 |Anaconda 4.2.0 (64-bit)  
boost 1.73  
  
I try to build the boost lib with a python 3.5 from an anaconda3.  
  
I bootstrap the boost:  
`./bootstrap.sh --prefix=$HOME/boost`  
It detects the correct python:  
  
    Building Boost.Build engine with toolset gcc... tools/build/src/engine/b2  
    Detecting Python version... 3.5  
    Detecting Python root... /home/joergboe/anaconda3  
    Unicode/ICU support for Boost.Regex?... /usr  
    Generating Boost.Build configuration in project-config.jam for gcc...  
  
Then I start the build:  
`./b2 -j8 --with-python --build-dir=$HOME/boost/build --stagedir=$HOME/boost/statge -q stage`  
The build fails:  
  
    In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/long.hpp:8,  
                 from libs/python/src/long.cpp:5:  
    ./boost/python/detail/wrap_python.hpp:57:23: fatal error: pyconfig.h: No such file or directory  
     # include <pyconfig.h>  
                           ^  
    compilation terminated.  
  
        "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/home/joergboe/anaconda3/include/python3.5" -c -o "/home/joergboe/boost/build/boost/bin.v2/libs/python/build/gcc-4.8.5/release/python-3.5/threading-multi/visibility-hidden/long.o" "libs/python/src/long.cpp"  
  
    ...failed gcc.compile.c++ /home/joergboe/boost/build/boost/bin.v2/libs/python/build/gcc-4.8.5/release/python-3.5/threading-multi/visibility-hidden/long.o...  
  
Apparently the automatic python configuration expects the python include path at:  
`/home/joergboe/anaconda3/include/python3.5"`  
but the real include path is:  
`/home/joergboe/anaconda3/include/python3.5m`  
  
The python installation reports the include path correctly:  
  
    python3-config --includes  
    -I/home/joergboe/anaconda3/include/python3.5m -I/home/joergboe/anaconda3/include/python3.5m  
  
The same with python:  
  
    python -c "import sysconfig; print(sysconfig.get_path('include'));"  
    /home/joergboe/anaconda3/include/python3.5m  
  
Similar issues were reported several times:  
#443   
#289   
  
Is there a solution ?

---

## Comment 1

> Username: TDCRich  
> Created at: 2020-10-28 17:29:36 UTC  
> Url: https://github.com/boostorg/build/issues/633#issuecomment-718091767  

I have a similar problem.  I'm trying to build on Amazon Linux 2.  It defaults to including python 2.7, but I've installed Python 3.7, and informed Boost's bootstrap program about this preferred Python.  But it's failing because it's still attempting to build libraries for Python2.7, and I don't even have the development files installed for that.  So, as above, it can't find pyconfig.h.  But I told it to use 3.7, and gave it the include and lib paths to boot.  
  
The way Python is installed on this is that /usr/bin/python is 2.7, and /usr/bin/python3 is 3.7.  Somewhere I haven't found, it's just using the "python" default and not using the "using" declarations I gave it.  HELP!

---

## Comment 2

> Username: TDCRich  
> Created at: 2020-10-28 17:31:35 UTC  
> Url: https://github.com/boostorg/build/issues/633#issuecomment-718092937  

Sorry, the version I'm building is boost_1_74_0

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:48 UTC  
> Url: https://github.com/boostorg/build/issues/633#issuecomment-850868209  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
