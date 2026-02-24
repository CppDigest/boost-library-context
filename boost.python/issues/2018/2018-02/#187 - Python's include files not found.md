# #187 - Python's include files not found [Closed]

> Username: matlo607  
> Created at: 2018-02-23 15:56:00 UTC  
> Updated at: 2022-09-29 06:26:31 UTC  
> Closed at: 2018-04-09 01:21:17 UTC  
> Url: https://github.com/boostorg/python/issues/187  

I am trying to build boost-python-1.66.0 on RedHat 6.4 and it seems that bjam sets a wrong include path on the command line :  
```  
"/opt/rh/devtoolset-3/root/usr/bin/g++"   -fpic -std=c++11 -O3 -finline-functions -Wno-inline -Wall -pthread -fPIC -m64 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG -D_GLIBCXX_USE_CXX11_ABI=1  -I"." -I"/opt/rh/rh-python34/root/usr/include/python3.4" -c -o "/root/.conan/data/boost/1.66.0/matt/testing/build/0597ba4e0c2f9c3af9dbbfd230307aa07a3d007a/boost/bin.v2/libs/python/build/gcc-gnu-4.9/rls/thrdp-pthrd/thrd-mlt/object/function_doc_signature.o" "libs/python/src/object/function_doc_signature.cpp"  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/converter/registrations.hpp:8,  
                 from libs/python/src/object/function_doc_signature.cpp:9:  
./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
 # include <pyconfig.h>  
                       ^  
compilation terminated.  
```  
  
I installed the following packages : `rh-python34` and `rh-python34-python-devel`.  
The issue must be reproducible on CentOS 6.4  
Actually `pyconfig.h` is located in /opt/rh/rh-python34/root/usr/include/python3.4**m** and not in /opt/rh/rh-python34/root/usr/include/python3.4  
  
If you run this script below you can see that the include path is correctly set.  
```python  
from sysconfig import get_paths  
from pprint import pprint  
info = get_paths()  
pprint(info)  
```  
```  
python3.4  
Python 3.4.2 (default, Aug  5 2016, 10:03:09)   
[GCC 4.4.7 20120313 (Red Hat 4.4.7-9)] on linux  
Type "help", "copyright", "credits" or "license" for more information.  
>>> from sysconfig import get_paths  
>>> from pprint import pprint  
>>> info = get_paths()  
>>> pprint(info)  
{'data': '/opt/rh/rh-python34/root/usr',  
 'include': '/opt/rh/rh-python34/root/usr/include/python3.4m',  
 'platinclude': '/opt/rh/rh-python34/root/usr/include/python3.4m',  
 'platlib': '/opt/rh/rh-python34/root/usr/lib64/python3.4/site-packages',  
 'platstdlib': '/opt/rh/rh-python34/root/usr/lib64/python3.4',  
 'purelib': '/opt/rh/rh-python34/root/usr/lib/python3.4/site-packages',  
 'scripts': '/opt/rh/rh-python34/root/usr/bin',  
 'stdlib': '/opt/rh/rh-python34/root/usr/lib64/python3.4'}  
```  
  
I had a look into `libs/python/config/python.py`. I guess this is how bjam gets the include path.  
```python  
# l.38  
def check_sysconfig(cmd):  
     r = check_python('import distutils.sysconfig as c; print(c.%s)'%cmd)  
     return r if r != 'None' else ''  
  
context.Message('Checking for Python...')  
python = context.env.GetOption('python') or 'python'  
context.env['PYTHON'] = python  
incpath = check_sysconfig('get_python_inc()')  
```  
When I run this in the python interpreter, I get /opt/rh/rh-python34/root/usr/include/python3.4**m** and not /opt/rh/rh-python34/root/usr/include/python3.4  
```python  
>>> import distutils.sysconfig as c  
>>> print (c.get_python_inc())  
/opt/rh/rh-python34/root/usr/include/python3.4m  
```  
Do you have any idea how this **m** disappeared ?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-02-23 16:10:58 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-368054951  

Matthieu,  
  
a simple way to fix your build issue is to explicitly define a "python" tool in your ~/user-config.jam file. Add a line such as  
```  
using python : 3.4 : python3 : /opt/rh/rh-python34/root/usr/include/python3.4m : /opt/rh/rh-python34/root/usr/lib64/libpython3.4m.so ;  
```  
to explicitly set the paths.  
The logic to figure out paths and flags is in https://github.com/boostorg/build/blob/develop/src/tools/python.jam. I don't know why the right path isn't found in your case. I'll look into it...

---

## Comment 2

> Username: matlo607  
> Created at: 2018-02-23 17:12:25 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-368074251  

The issue seems to come from line 547. The include path is not computed in the same way than it is done in `libs/python/config/python.py`  
```  
includes ?= $(prefix)/include/python$(version) ;  
```

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-02-23 17:22:58 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-368077746  

Indeed ! Do you mind filing an issue with the Boost.Build module (https://github.com/boostorg/build/issues) ? I myself have only limited understanding of the official Boost build system (and for that reason have forked it into http://stefan.seefeld.name/faber/).  
Thanks !

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-04-09 01:21:17 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-379601001  

Since I haven't heard anything since my last comment, I assume this is being taken care of by the `Boost.Build` project.

---

## Comment 5

> Username: HDembinski  
> Created at: 2018-07-03 21:24:16 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-402296228  

I just checked that the problem still persists when you try to compile  
Boost-1.65.0  
with b2 on travis in trusty with python3.5. Could you please reopen the issue?

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2018-07-03 21:26:20 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-402296703  

We are about to release version *1.68*. To verify you should use the current master (and soon a release candidate, whenever that gets built). So unless you tell me that the current master is still broken, I don't see any need to reopen this issue.

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-07-03 21:27:32 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-402297007  

Why not add a test on travis to check this?  
The same issue can be found here #68  
And here are desperate requests for fixes.  
https://svn.boost.org/trac10/ticket/11120#comment:21

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2018-07-03 21:29:25 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-402297426  

Sorry, my previous reply was meant for a different issue.  
Still, as I pointed out, this is not actually an issue with `Boost.Python`, but rather `Boost.Build`, so please open an issue there if this is still a problem.

---

## Comment 9

> Username: HDembinski  
> Created at: 2018-07-03 21:36:03 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-402298968  

Ok, good point. Thank you for the quick response. :)

---

## Comment 10

> Username: HDembinski  
> Created at: 2018-07-03 21:41:47 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-402300245  

Just for the record, the bug is still there. The faulty line pointed out by @matlo607 is still the same in the current develop branch.  
> The issue seems to come from line 547. The include path is not computed in the same way than it is done in libs/python/config/python.py  
```  
includes ?= $(prefix)/include/python$(version) ;  
```

---

## Comment 11

> Username: HDembinski  
> Created at: 2018-07-03 21:48:30 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-402301786  

Open PR in Boost.Build to fix the issue:  
https://github.com/boostorg/build/pull/250

---

## Comment 12

> Username: dickreuter  
> Created at: 2019-11-30 06:54:09 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-559918434  

The issue is still there with windows

---

## Comment 13

> Username: rajhlinux  
> Created at: 2022-09-29 06:26:31 UTC  
> Url: https://github.com/boostorg/python/issues/187#issuecomment-1261823708  

It's 2022, and the problem still priests, (using FreeBSD 13.1)... :man_facepalming:
