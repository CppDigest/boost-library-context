# #369 - Confusion over boost::python installation instructions - no bjam [Open]

> Username: Rogan-Clark  
> Created at: 2021-07-21 12:31:48 UTC  
> Updated at: 2021-07-22 20:06:49 UTC  
> Url: https://github.com/boostorg/python/issues/369  

Hi. I'm trying to install boost::python, following the installation instructions given here for boost 1.76.0 (https://www.boost.org/doc/libs/1_76_0/libs/python/doc/html/building/no_install_quickstart.html).  
  
It references Section 5 of the initial install documentation here (https://www.boost.org/doc/libs/1_76_0/more/getting_started/unix-variants.html), asking you to use that to install bjam, then to use bjam to bootstrap the boost:python build. The problem is that, following the instructions as given in Section 5, then attempting to use bjam results in my terminal telling me bjam isn't installed. Furthermore, reading the documentation for version 1.76.0 here (https://www.boost.org/doc/libs/1_76_0/tools/build/doc/html/index.html#bbv2.faq.projectroot) seems to imply bjam no longer exists in boost, and has been supplanted by the b2 installer referenced in the earlier install. Am I missing something to get boost.python working?   
  
Apologies if I'm missing something simple.  
  
Rogan

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2021-07-21 13:28:02 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884189954  

Hi Rogan,  
the documentation you are referring to is horribly outdated, I'm afraid. I'm sorry for that !  
  
Can you please describe in a bit more  detail what you want to do ? Do you want to build Boost.Python and all its prerequisites from a Boost source package ? Or do you already have prerequisite Boost libraries installed and want only to compile Boost.Python ?  
What platform are you on (OS, compiler, etc.) ?  
  
The best way forward will depend on the answers to these questions.

---

## Comment 2

> Username: Rogan-Clark  
> Created at: 2021-07-21 13:45:41 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884202283  

I'm on CentOS 7, attempting to build Boost.Python from source along with the rest of Boost.   
  
And no worries, I'm used to documentation being a little dodgy, that's why I asked!  
  
Rogan

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2021-07-21 13:54:11 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884208575  

In that case, I think the best option is to simply follow https://www.boost.org/doc/libs/1_76_0/more/getting_started/unix-variants.html#easy-build-and-install, i.e.  
```  
$ ./bootstrap.sh --prefix=path/to/installation/prefix  
$ ./b2 install  
```  
with optional arguments added to restrict what to build (to only build Boost.Python, add `--with-libraries=python`.  
You may also want to configure what Python version to build with, see https://www.boost.org/doc/libs/1_76_0/libs/python/doc/html/building/configuring_boost_build.html#building.configuring_boost_build.python_configuration_parameters

---

## Comment 4

> Username: Rogan-Clark  
> Created at: 2021-07-21 14:10:27 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884221183  

Okay, cool, I'll give that a try 👍

---

## Comment 5

> Username: Rogan-Clark  
> Created at: 2021-07-22 12:15:03 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884865701  

So I've tried that, building just the python libraries (using --with-libraries=python). While the basic headers are copied over, the python specific files seem to fail with this repeating error message.  
  
gcc.compile.c++ bin.v2/libs/python/build/gcc-7.3.0/release/python-3.6/threading-multi/visibility-hidden/list.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/list.hpp:8,  
                 from libs/python/src/list.cpp:5:  
./boost/python/detail/wrap_python.hpp:57:11: fatal error: pyconfig.h: No such file or directory  
 # include <pyconfig.h>  
           ^~~~~~~~~~~~  
compilation terminated.  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/usr/include/python3.6"  -c -o "bin.v2/libs/python/build/gcc-7.3.0/release/python-3.6/threading-multi/visibility-hidden/list.o" "libs/python/src/list.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-7.3.0/release/python-3.6/threading-multi/visibility-hidden/list.o...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-7.3.0/release/python-3.6/threading-multi/visibility-hidden/long.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/long.hpp:8,  
                 from libs/python/src/long.cpp:5:  
./boost/python/detail/wrap_python.hpp:57:11: fatal error: pyconfig.h: No such file or directory  
  
The filepath seems to exist manually checking, so I'm unsure of the problem.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2021-07-22 12:18:15 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884867581  

So for avoidance of doubt, does `/usr/include/python3.6/pyconfig.h` exist ?

---

## Comment 7

> Username: Rogan-Clark  
> Created at: 2021-07-22 12:21:18 UTC  
> Updated at: 2021-07-22 12:22:06 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884869193  

Annoyingly no, it seems to be under ```/usr/include/python3.6m/pyconfig.h instead``` (I'm not sure what the m's doing there). There is a python3.6/ directory, but no pyconfig.h inside it

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2021-07-22 12:22:34 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884869903  

Then you need to adjust your configuration (in `~/user-config.jam`)

---

## Comment 9

> Username: Rogan-Clark  
> Created at: 2021-07-22 12:34:59 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-884877007  

I can't find a file by that name, sorry - do you mean ```project-config.jam```?

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2021-07-22 20:06:48 UTC  
> Url: https://github.com/boostorg/python/issues/369#issuecomment-885198202  

Please follow instructions from https://boostorg.github.io/python/doc/html/building/configuring_boost_build.html
