# #265 - Giving default value to arg causes segfault [Closed]

> Username: stuarteberg  
> Created at: 2019-04-24 14:11:20 UTC  
> Updated at: 2019-10-04 18:30:26 UTC  
> Closed at: 2019-04-24 16:04:13 UTC  
> Url: https://github.com/boostorg/python/issues/265  

On my Mac, the following program segfaults as soon as the module is imported:  
  
```c++  
#include <boost/python.hpp>  
  
char const* greet(int x)  
{  
   return "hello, world";  
}  
  
BOOST_PYTHON_MODULE(testboost)  
{  
    using namespace boost::python;  
    def("greet", greet, (arg("x")=0));  
}  
```  
  
(I tested with boost-1.68 and 1.69.  I haven't checked 1.70 yet.)  
  
The issue seems related to, but not quite the same as #38.  Inspired by the comments in that issue, I wrote an even more minimal program that produces a segfault.  Merely using `arg("x")=0` induces a segfault.  
  
```c++  
#include <boost/python.hpp>  
  
int main() {  
    using namespace boost::python;  
    arg x("x");  
  
    x=0;  // segfault  
    //x=99; // segfault  
      
    //x=0.0;   // no segfault  
    //x="";    // no segfault  
    //x=false; // no segfault  
      
    return 0;  
}  
```  
  
<details>  
  
<summary>compiler and OS details</summary>  
  
```  
$ clang --version  
Apple LLVM version 8.1.0 (clang-802.0.42)  
Target: x86_64-apple-darwin16.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
  
$ system_profiler SPSoftwareDataType  
Software:  
  
    System Software Overview:  
  
      System Version: macOS 10.12.6 (16G1408)  
      Kernel Version: Darwin 16.7.0  
      Boot Volume: Mac HD  
      Boot Mode: Normal  
      Computer Name: bergs-lm3  
      User Name: Berg, Stuart (bergs)  
      Secure Virtual Memory: Enabled  
      System Integrity Protection: Disabled  
      Time since boot: 5 days 19:31  
```  
  
</details>  
  
**Edit:** I can also reproduce the segfault on Linux, using Anaconda's gcc-7 compiler:  
  
<details>  
  
<summary>gcc details and example invocation</summary>  
  
```  
$ x86_64-conda_cos6-linux-gnu-g++ --version  
x86_64-conda_cos6-linux-gnu-g++ (crosstool-NG 1.23.0.449-a04d0) 7.3.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
  
$ conda list  
# packages in environment at /opt/conda/envs/testboost:  
#  
# Name                    Version                   Build  Channel  
binutils_impl_linux-64    2.31.1               h6176602_1    defaults  
binutils_linux-64         2.31.1               h6176602_3    defaults  
boost                     1.69.0          py36h8619c78_1001    conda-forge  
boost-cpp                 1.69.0            h11c811c_1000    conda-forge  
bzip2                     1.0.6             h14c3975_1002    conda-forge  
ca-certificates           2019.3.9             hecc5488_0    conda-forge  
certifi                   2019.3.9                 py36_0    conda-forge  
gcc_impl_linux-64         7.3.0                habb00fd_1    conda-forge  
gcc_linux-64              7.3.0                h553295d_3    conda-forge  
gxx_impl_linux-64         7.3.0                hdf63c60_1    conda-forge  
gxx_linux-64              7.3.0                h553295d_3    conda-forge  
icu                       58.2              hf484d3e_1000    conda-forge  
libblas                   3.8.0                6_openblas    conda-forge  
libcblas                  3.8.0                6_openblas    conda-forge  
libffi                    3.2.1             he1b5a44_1006    conda-forge  
libgcc-ng                 8.2.0                hdf63c60_1    defaults  
libgfortran-ng            7.2.0                hdf63c60_3    conda-forge/label/cf201901  
liblapack                 3.8.0                6_openblas    conda-forge  
libstdcxx-ng              8.2.0                hdf63c60_1    defaults  
ncurses                   6.1               hf484d3e_1002    conda-forge  
numpy                     1.16.3           py36he5ce36f_0    conda-forge  
openblas                  0.3.5             h9ac9557_1001    conda-forge  
openssl                   1.1.1b               h14c3975_1    conda-forge  
pip                       19.1                     py36_0    conda-forge  
python                    3.6.7             h381d211_1004    conda-forge  
readline                  7.0               hf8c457e_1001    conda-forge  
setuptools                41.0.1                   py36_0    conda-forge  
sqlite                    3.26.0            h67949de_1001    conda-forge  
tk                        8.6.9             h84994c4_1001    conda-forge  
wheel                     0.33.1                   py36_0    conda-forge  
xz                        5.2.4             h14c3975_1001    conda-forge  
zlib                      1.2.11            h14c3975_1004    conda-forge  
  
$ cat main.cpp  
#include <boost/python.hpp>  
  
int main() {  
    using namespace boost::python;  
    arg x("x");  
  
    x=0;  // segfault  
    //x=99; // segfault  
  
    //x=0.0;   // no segfault  
    //x="";    // no segfault  
    //x=false; // no segfault  
  
    return 0;  
}  
  
$ x86_64-conda_cos6-linux-gnu-g++   -I${CONDA_PREFIX}/include   -I${CONDA_PREFIX}/include/python3.6m   -fPIC   -L${CONDA_PREFIX}/lib   -lpython3.6m   -lboost_python36   -o main.out main.cpp  
  
$ ./main.out  
Segmentation fault  
```  
  
</details>

---

## Comment 1

> Username: stuarteberg  
> Created at: 2019-04-24 15:39:20 UTC  
> Url: https://github.com/boostorg/python/issues/265#issuecomment-486295900  

FWIW, here's what lldb's backtrace says when running `main()`:  
  
```  
$ lldb -- ./main.out  
(lldb) target create "./main.out"  
Current executable set to './main.out' (x86_64).  
  
(lldb) run  
Process 47207 launched: './main.out' (x86_64)  
Process 47207 stopped  
* thread #1, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x30)  
    frame #0: 0x000000010000144c main.out`void boost::python::xdecref<_object>(_object*) + 188  
main.out`boost::python::xdecref<_object>:  
->  0x10000144c <+188>: movq   0x30(%rax), %rax  
    0x100001450 <+192>: movq   -0x18(%rbp), %rdi  
    0x100001454 <+196>: callq  *%rax  
    0x100001456 <+198>: jmp    0x10000145b               ; <+203>  
  
(lldb) bt  
* thread #1, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x30)  
  * frame #0: 0x000000010000144c main.out`void boost::python::xdecref<_object>(_object*) + 188  
    frame #1: 0x0000000100001388 main.out`boost::python::handle<_object>::~handle() + 24  
    frame #2: 0x0000000100001365 main.out`boost::python::handle<_object>::~handle() + 21  
    frame #3: 0x0000000100001349 main.out`boost::python::detail::keyword::~keyword() + 25  
    frame #4: 0x00000001000012b5 main.out`boost::python::detail::keyword::~keyword() + 21  
    frame #5: 0x00000001000014c3 main.out`boost::python::detail::keywords_base<1ul>::~keywords_base() + 51  
    frame #6: 0x0000000100001485 main.out`boost::python::detail::keywords<1ul>::~keywords() + 21  
    frame #7: 0x0000000100000f15 main.out`boost::python::detail::keywords<1ul>::~keywords() + 21  
    frame #8: 0x0000000100000d53 main.out`main + 83  
    frame #9: 0x00007fffb9788235 libdyld.dylib`start + 1  
  
(lldb)  
```

---

## Comment 2

> Username: stuarteberg  
> Created at: 2019-04-24 16:04:13 UTC  
> Url: https://github.com/boostorg/python/issues/265#issuecomment-486307144  

I'm using conda-forge to supply my build of both python and boost, and there seems to be a difference between their old builds and their more recent ones.  Until I can pin down the differences, I'm closing this for now.

---

## Comment 3

> Username: ndevenish  
> Created at: 2019-10-04 18:17:22 UTC  
> Url: https://github.com/boostorg/python/issues/265#issuecomment-538507258  

I just ran into this exact issue building our software with conda. I was accidentally linking to the system libpython (was weak linking on mac, but for some reason the linker was hard-linking with an rpath also) instead of the conda one. the problem seemed to go away if I just changed to leave symbols unresolved.

---

## Comment 4

> Username: stuarteberg  
> Created at: 2019-10-04 18:28:37 UTC  
> Updated at: 2019-10-04 18:30:26 UTC  
> Url: https://github.com/boostorg/python/issues/265#issuecomment-538511278  

Yeah, at some point they (Anaconda and also conda-forge) started statically linking the python executable, which means that all symbols from `libpython.so` are already present within `bin/python` itself.  If you also link against `libpython.so`, you'll see weird issues and segfaults related to the mismatch between the code compiled into `python` vs `libpython`.  
  
As a result, it's a mistake to link your module against any version `libpython.so` (even the one from conda) -- you should leave those symbols unresolved until runtime.  (On Mac, that means use `-undefined dynamic_lookup`, and don't use `-lpython`.  On Linux, simply omitting `-lpython` is enough.)  
  
I don't specifically remember how I fixed my issue described above, but I suspect it was by leaving the symbols unresolved, as you say.
