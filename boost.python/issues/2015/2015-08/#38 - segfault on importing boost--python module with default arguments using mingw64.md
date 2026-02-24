# #38 - segfault on importing boost::python module with default arguments using mingw64 [Closed]

> Username: carsten-forty2  
> Created at: 2015-08-26 17:59:11 UTC  
> Updated at: 2015-08-28 17:09:01 UTC  
> Closed at: 2015-08-28 17:09:01 UTC  
> Url: https://github.com/boostorg/python/issues/38  

I experience a segfault on importing the module resulting from the code snippet below:  
Environment: Windows7, msys2, mingw64-gcc-5.2. <=boost-1.59.0  
  
No Problems occur for win32 and under linux 64/32bit or if I remove the default parameter inside the declartion.  
  
```  
#include <boost/python.hpp>  
#include <boost/python/def.hpp>  
  
using namespace boost::python;  
  
 inline int f(double x, double y=1.0){return 33;}  
  
BOOST_PYTHON_MODULE(_bp_)  
{  
   def("f", f, (arg("x"), arg("y") = 1.0));  
}  
```  
### Compiled with:  
  
ARCH=64  
GCC=/C/msys64/mingw64/bin/g++.exe  
PYROOT=../../thirdParty/WinPython-$(ARCH)bit-3.4.3.3/python-3.4.3.amd64/  
PYLIB=$(PYROOT)/libs/libpython34.dll.a  
  
$(GCC) -O2 -Os -pipe -ansi -Wno-unused-local-typedefs $(INC) -o bp.obj -c bp.cpp  
$(GCC) -shared -o _bp_.pyd bp.obj $(PYLIB) \  
$(BOOSTROOT)/lib/libboost_python-mt.dll \  
$(BOOSTROOT)/lib/libboost_system-mt.dll   
  
My libboost_python*.dll are compiled with the same compiler and linked again my local WinPython.  
  
or:  
  
```  
/C/msys64/mingw64/bin/g++.exe -O2 -Os -pipe -ansi -Wno-unused-local-typedefs -I ../gimli/thirdParty/dist-GNU-5.2.0-64/boost_1_59_0-gcc-5.2.0-64-py34/include/ -I ../../thirdParty/WinPython-64bit-3.4.3.3/python-3.4.3.amd64//include -o bp.obj -c bp.cpp  
/C/msys64/mingw64/bin/g++.exe -shared -o _bp_.pyd bp.obj ../../thirdParty/WinPython-64bit-3.4.3.3/python-3.4.3.amd64//libs/libpython34.dll.a ../gimli/thirdParty/dist-GNU-5.2.0-64/boost_1_59_0-gcc-5.2.0-64-py34/lib/libboost_python-mt.dll  ../gimli/thirdParty/dist-GNU-5.2.0-64/boost_1_59_0-gcc-5.2.0-64-py34/lib/libboost_system-mt.dll  
```  
### Testet with:  
  
```  
python -c 'import _bp_; print(_bp_.f(1, 2))'  
```  
  
If I remove the default parameter '=1.0' inside the def declaration everthing works as expected.  
There is also no problem with default arguments if the arg 'y' is of type int.   
  
Are there any ideas how to overcome this problem unless removing all default arguments in my whole c++ library?  
  
Bests,  
Carsten

---

## Comment 1

> Username: hotgloupi  
> Created at: 2015-08-27 11:24:59 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135393282  

A simple workaround is to bind two version of your function:  
  
```  
def("f", static_cast<int (*)(double)>(&f))  
def("f", static_cast<int (*)(double, double)>(&f))  
```  
  
This is a little cumbersome, but it avoids a discrepancy between the default arguments in the bindings and in the c++ code.

---

## Comment 2

> Username: carsten-forty2  
> Created at: 2015-08-27 12:01:45 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135400900  

I thought about this .. but my binding code is generated automatic via pyplusplus and I have no Idea about the effort hacking these package ..   
  
I wonder if this segfault is a sign for a depper problem since it works smooth for linux and win32.  
  
btw. What do you mean by discrepancy?  
  
even if a change it to 1 arg and do a cast:  
  
```  
inline int f(double x=1.0){return 33;}  
  
BOOST_PYTHON_MODULE(_bp_)  
{  
def("f", f, (arg("x") = (double)1.0));  
}  
```  
  
the import segfaults.  
  
Bests

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2015-08-27 12:12:17 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135403660  

On 27/08/15 08:01 AM, carsten-forty2 wrote:  
  
> I thought about this .. but my binding code is generated automatic via  
> pyplusplus and I have no Idea about the effort hacking these package ..  
>   
> I wonder if this segfault is a sign for a depper problem since it  
> works smooth for linux and win32.  
  
The deeper problem may be a compiler bug, really. To find out you'd have  
to step through the debugger.  
Unfortunately I'm not able to help as I don't have Windows anywhere near.  
  
##   
  
```  
  ...ich hab' noch einen Koffer in Berlin...  
```

---

## Comment 4

> Username: carsten-forty2  
> Created at: 2015-08-27 12:20:58 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135405353  

windows is also not my dev platform.  
  
The same problem also occur clang++  
  
I have no idea how to debug this python import .. should gdb work?  
  
##   
  
Grettings from Berlin.

---

## Comment 5

> Username: hotgloupi  
> Created at: 2015-08-27 12:44:51 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135410404  

@carsten-forty2   
  
By discrepancy I meant that the default value that you set in your bindings could be different than the one in the function signature.  
But since you generate the bindings, you won't have that problem :)

---

## Comment 6

> Username: carsten-forty2  
> Created at: 2015-08-27 13:10:44 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135420719  

Even more strange. I simplified the snipet and avoid the python import.  
  
```  
 #include <boost/python.hpp>  
 #include <boost/python/def.hpp>  
  
 int main(){  
    arg a("x");  
    a=1; // works  
    a=1.0; // segfaults  
    exit(0);  
}  
```  
  
I wish I had valgrind on windows. *sigh

---

## Comment 7

> Username: carsten-forty2  
> Created at: 2015-08-27 15:28:15 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135469311  

The problem is probably related to my compiled boost_python using WinPython64.  
  
If I use native mingw64-python3 with mingw64-boost_python the problem disappear.  
  
Unfortunataly, this ist not yet a solution since I need an extension to WinPython.   
  
Maybee I miss some mysterious DEFINE setting for the compilation of boost_python.

---

## Comment 8

> Username: carsten-forty2  
> Created at: 2015-08-27 18:18:34 UTC  
> Url: https://github.com/boostorg/python/issues/38#issuecomment-135512746  

hmm .. if I compile with mingw64-python-headers but link against WinPython .. the example runs through.  I have to look at the differences now.   
  
So the problem seems not related to boost-python.  
  
TY
