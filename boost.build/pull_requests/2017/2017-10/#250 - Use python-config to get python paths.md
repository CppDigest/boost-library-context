# #250 Use python-config to get python paths [Closed]

> Username: ghost  
> Created at: 2017-10-05 11:13:21 UTC  
> Updated at: 2021-10-02 21:01:43 UTC  
> Closed at: 2019-07-30 23:13:00 UTC  
> Url: https://github.com/boostorg/build/pull/250  

Sometimes python appends additional letters after a version number in its paths (for example `/usr/inlude/python3.6m`). This caused an error when building `boost_python` with default configuration, as described on [https://svn.boost.org/trac10/ticket/11120](https://svn.boost.org/trac10/ticket/11120). The patch enables `python-config` command to determine library and include paths.

---

## Comment 1

> Username: tkelman  
> Created_at: 2018-02-05 18:49:17 UTC  
> Updated_at: 2018-03-27 21:41:07 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-363182527  

There seem to be some python 3 environments (inside a virtualenv, for example) that have a `python-config` but not a `python3.6-config` executable. I was able to use the patch from https://svn.boost.org/trac10/attachment/ticket/11120/python_jam.patch, though `sys.abiflags` isn't available in python 2 so it throws a nuisance warning.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2018-07-03 22:00:51 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-402304653  

As this is a long-standing issue which dates back three years  
https://svn.boost.org/trac10/ticket/11120#comment:21  
It is great to see an attempt to solve this. Since there are issues with the solution based on `python-config`, as pointed out by @tkelman , let me propose another solution.  
According to my research, the safest way of getting python paths is via python itself. The following two code snippets print the python include path and the python library, by pulling the information out of distutils.  
```  
# get python include path  
from distutils import sysconfig  
import sys  
sys.stdout.write(sysconfig.get_python_inc())  
```  
  
```  
# get python library  
from distutils import sysconfig  
import os.path as op  
import sys  
v = sysconfig.get_config_vars()  
fpaths = [op.join(v[pv], v['LDLIBRARY']) for pv in ('LIBDIR', 'LIBPL')]  
sys.stdout.write(list(filter(op.exists, fpaths))[0])  
```  
  
I use these scripts in https://github.com/hdembinski/histogram which compiles successfully on Travis with Python 2.7 and 3.6.

---

## Comment 3

> Username: tkelman  
> Created_at: 2018-07-03 22:11:27 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-402306927  

python-config itself isn't that much more complicated (https://github.com/pypa/virtualenv/blob/master/virtualenv_embedded/python-config) - the hard part can be what name it goes by. The built-in version of `python3-config` looks slightly different, but virtualenv may be making do with a single source across all python versions?

---

## Comment 4

> Username: HDembinski  
> Created_at: 2018-07-03 23:02:52 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-402315846  

The output of the Python snippets requires no parsing, though, while there is a good deal of sed regex used in this patch to retrieve the info from python-config. The Python solution is easier, IMHO.

---

## Comment 5

> Username: tkelman  
> Created_at: 2018-07-03 23:08:20 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-402316850  

Sure, implementing this by asking sysconfig isn't a bad solution, but if you're going to use that approach I'd recommend being consistent with exactly what python-config would return. That's much better tested across various different python build configurations, distros etc than returning the first library that exists.

---

## Comment 6

> Username: HDembinski  
> Created_at: 2018-07-04 09:54:35 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-402428915  

Fair point. So perhaps the maintainers could comment? Should I submit a separate PR addressing the same issue?

---

## Comment 7

> Username: jefftrull  
> Created_at: 2019-05-22 19:09:36 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-494928836  

Please do merge this.... or some similar solution.

---

## Comment 8

> Username: marcovzla  
> Created_at: 2019-07-30 22:10:05 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-516615234  

Please merge this or solve this issue somehow

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2019-07-30 23:11:26 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-516629975  

Sorry I've ignore the b2 side of python-dec config for so long. I have been working on an appropriately portable solution to the problem in the context of Conan for some time now. Which you can see here https://github.com/bincrafters/conan-python_dev_config/blob/stable/0.6/conanfile.py -- That one has ben proven to work reliably in many different environments now. Through both user experience and varied CI testing. At some point soon I'll convert that method to b2 as a rewrite to the `python.jam` file. If someone else wants to attempt to do that "port" sooner I'd welcome it though.

---

## Comment 10

> Username: filips123  
> Created_at: 2020-08-05 19:16:59 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-669424443  

What is current state of this? It seems the issue is still present on Boost Build 1.73.0, tested on GitHub Actions with macOS and Python 3.5, but most likely also on other systems. Include path is `/Library/Frameworks/Python.framework/Versions/3.5/include/python3.5` while it should be `/Library/Frameworks/Python.framework/Versions/3.5/include/python3.5m`.  
  
Although include path in Python 3.8 is again `python3.8` without `m`, this still prevents me from building on older versions.

---

## Comment 11

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:42 UTC  
> Url: https://github.com/boostorg/build/pull/250#issuecomment-932819949  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
