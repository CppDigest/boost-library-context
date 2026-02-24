# #248 - Python 3.6 + boost::python::str results in Bus error at end of Python process [Open]

> Username: ysnt27  
> Created at: 2019-02-06 10:00:58 UTC  
> Updated at: 2019-10-28 02:50:14 UTC  
> Url: https://github.com/boostorg/python/issues/248  

On Python 3.6,  when boost::python::str constructor is used at global scope, I receive "Bus error" at end of python process.  
  
Python 3.4 and 3.5 worked fine, no errors.  
  
### My environment  
  
- CentOS Linux release 7.6.1810 (Core)  
- `x86_64`  
- `boost-1_69_0`  
- Python 3.6 from `rh-python36-python-3.6.3-3.el7.x86_64` and Anaconda  
  
### Source code  
  
```c++  
#include <boost/python.hpp>  
#include <boost/python/str.hpp>  
  
static const boost::python::str aaa("AAA BBB CCC");  
  
  
boost::python::str get() {  
    return aaa;  
}  
  
BOOST_PYTHON_MODULE(teststr) {  
    using namespace boost::python;  
    def("get", &get);  
}   
```  
  
  
### Compile  
  
```  
% g++ -g -DPIC -shared -fpic -o teststr.so teststr.cc -L/usr/local/boost-1_69_0-py36/lib -lboost_python36 -I/opt/rh/rh-python36/root/usr/include/python3.6m -I/usr/local/boost-1_69_0-py36/include -Wl,-rpath,/usr/local/boost-1_69_0-py36/lib  
```  
  
### Run  
  
Prepare test python script.  
  
```  
% cat >ttt.py  
import teststr  
print(teststr.get())  
```  
  
```  
% /opt/rh/rh-python36/root/usr/bin/python3.6m ttt.py  
AAA BBB CCC  
Segmentation fault (core dumped)  
```  
  
with gdb  
  
```  
% gdb /opt/rh/rh-python36/root/usr/bin/python3.6m   
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-114.el7  
Copyright (C) 2013 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"  
and "show warranty" for details.  
This GDB was configured as "x86_64-redhat-linux-gnu".  
For bug reporting instructions, please see:  
<http://www.gnu.org/software/gdb/bugs/>...  
Reading symbols from /opt/rh/rh-python36/root/usr/bin/python3.6m...Reading symbols from /opt/rh/rh-python36/root/usr/bin/python3.6m...(no debugging symbols found)...done.  
(no debugging symbols found)...done.  
Missing separate debuginfos, use: debuginfo-install rh-python36-python-3.6.3-3.el7.x86_64  
(gdb) run ttt.py  
Starting program: /opt/rh/rh-python36/root/usr/bin/python3.6m ttt.py  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib64/libthread_db.so.1".  
AAA BBB CCC  
  
Program received signal SIGBUS, Bus error.  
0x00007ffff6c4225e in _int_free (av=0x9094e203000000, p=0x7ffff7e97ba0,   
    have_lock=0) at malloc.c:3940  
3940	      (void)mutex_lock(&av->mutex);  
(gdb) where  
#0  0x00007ffff6c4225e in _int_free (av=0x9094e203000000, p=0x7ffff7e97ba0,   
    have_lock=0) at malloc.c:3940  
#1  0x00007ffff04930c1 in boost::python::api::object_base::~object_base (  
    this=0x7ffff0696478 <aaa>, __in_chrg=<optimized out>)  
    at /usr/local/boost-1_69_0-py36/include/boost/python/object_core.hpp:423  
#2  0x00007ffff0492f90 in boost::python::api::object::~object (  
    this=0x7ffff0696478 <aaa>, __in_chrg=<optimized out>)  
    at /usr/local/boost-1_69_0-py36/include/boost/python/object_core.hpp:238  
#3  0x00007ffff049313e in boost::python::detail::str_base::~str_base (  
    this=0x7ffff0696478 <aaa>, __in_chrg=<optimized out>)  
    at /usr/local/boost-1_69_0-py36/include/boost/python/str.hpp:28  
#4  0x00007ffff049317e in boost::python::str::~str (this=0x7ffff0696478 <aaa>,   
    __in_chrg=<optimized out>)  
    at /usr/local/boost-1_69_0-py36/include/boost/python/str.hpp:144  
#5  0x00007ffff6bfab69 in __run_exit_handlers (status=0,   
    listp=0x7ffff6f876c8 <__exit_funcs>,   
    run_list_atexit=run_list_atexit@entry=true) at exit.c:77  
#6  0x00007ffff6bfabb7 in __GI_exit (status=<optimized out>) at exit.c:99  
#7  0x00007ffff6be33dc in __libc_start_main (main=0x4009b0 <main>, argc=2,   
    argv=0x7fffffffe4d8, init=<optimized out>, fini=<optimized out>,   
    rtld_fini=<optimized out>, stack_end=0x7fffffffe4c8)  
    at ../csu/libc-start.c:300  
#8  0x0000000000400c40 in _start ()  
(gdb)   
```

---

## Comment 1

> Username: ycollet  
> Created at: 2019-03-15 13:12:28 UTC  
> Url: https://github.com/boostorg/python/issues/248#issuecomment-473280429  

I've got the same bug with python 3.7 and boost-1.69:  
```  
#0  dict_dealloc (mp=0x7fffeab5c328) at Objects/dictobject.c:1901  
#1  0x00007fffe9a2aa2f in boost::python::api::object_base::~object_base (this=0x7fffea9b56a0 <PyExecuter::main_namespace>, __in_chrg=<optimized out>)  
    at /home/artelys/repository/artelys/COE_BUILD/_dep/install/include/boost/python/object_core.hpp:423  
#2  0x00007fffe9a2a8fa in boost::python::api::object::~object (this=0x7fffea9b56a0 <PyExecuter::main_namespace>, __in_chrg=<optimized out>)  
    at /home/artelys/repository/artelys/COE_BUILD/_dep/install/include/boost/python/object_core.hpp:238  
#3  0x00007ffff7c68ccc in __run_exit_handlers () from /lib64/libc.so.6  
#4  0x00007ffff7c68e00 in exit () from /lib64/libc.so.6  
#5  0x00000000004dc995 in Py_Exit (sts=<optimized out>, sts@entry=0) at Python/pylifecycle.c:2282  
#6  0x00000000004e45a2 in handle_system_exit () at Python/pythonrun.c:636  
#7  0x00000000004e46ee in handle_system_exit () at Python/pythonrun.c:646  
#8  PyErr_PrintEx (set_sys_last_vars=set_sys_last_vars@entry=1) at Python/pythonrun.c:646  
#9  0x00000000004e4a5a in PyErr_Print () at Python/pythonrun.c:542  
#10 0x00000000004e54ac in PyRun_InteractiveLoopFlags (fp=fp@entry=0x7ffff7deea20 <_IO_2_1_stdin_>, filename_str=filename_str@entry=0x57e94e "<stdin>",   
    flags=flags@entry=0x7fffffffce0c) at Python/pythonrun.c:134  
#11 0x00000000004e5bee in PyRun_AnyFileExFlags (fp=fp@entry=0x7ffff7deea20 <_IO_2_1_stdin_>, filename=0x57e94e "<stdin>", closeit=closeit@entry=0,   
    flags=flags@entry=0x7fffffffce0c) at Python/pythonrun.c:78  
#12 0x0000000000430ae4 in pymain_run_file (p_cf=0x7fffffffce0c, filename=0x0, fp=0x7ffff7deea20 <_IO_2_1_stdin_>) at Modules/main.c:427  
#13 pymain_run_filename (cf=0x7fffffffce0c, pymain=0x7fffffffcee0) at Modules/main.c:1627  
#14 pymain_run_python (pymain=0x7fffffffcee0) at Modules/main.c:2876  
#15 pymain_main (pymain=pymain@entry=0x7fffffffcee0) at Modules/main.c:3037  
#16 0x0000000000430ce7 in _Py_UnixMain (argc=<optimized out>, argv=<optimized out>) at Modules/main.c:3072  
#17 0x00007ffff7c52413 in __libc_start_main () from /lib64/libc.so.6  
#18 0x000000000042bd6e in _start () at Objects/dictobject.c:818  
```  
  
Python is compiled from sources (3.7.2) on Fedora 29 and boost is compiled from sources too.   
I tested with boost from github repo and the gdb trace is the one above.

---

## Comment 2

> Username: ysnt27  
> Created at: 2019-10-25 05:19:31 UTC  
> Url: https://github.com/boostorg/python/issues/248#issuecomment-546204744  

I couldn't reproduce the problem with python-3.7.4 and boost-1.71.0 anymore.  
But from source code below, I got another segmentation fault around list_base destructor.  
  
``` cpp  
#include <boost/python.hpp>  
#include <boost/python/str.hpp>  
  
using namespace boost::python;  
  
class teststr3 {  
public:  
  
  static list aaa_list;  
  static const str aaa1;  
  static const str aaa2;  
  
  teststr3()  
  {  
    aaa_list.append(aaa1);  
    aaa_list.append(aaa2);  
  }  
  
  str get(int i) {  
    if (i==1) {  
      return aaa1;  
    } else {  
      return aaa2;  
    }  
  }  
};  
  
list teststr3::aaa_list;  
const str teststr3::aaa1("1 AAA BBB CCC");  
const str teststr3::aaa2("2 AAA BBB CCC");  
    
BOOST_PYTHON_MODULE(teststr3) {  
  using namespace boost::python;  
  class_<teststr3>("teststr3")  
    .def("get", &teststr3::get);  
}  
  
/*  
g++ -g -DPIC -shared -fpic -o teststr3.so teststr3.cc -L/usr/local/boost-1_71_0-py374/lib -lboost_python37 -I/usr/local/python374/include/python3.7dm -I/usr/local/boost-1_71_0-py374/include -Wl,-rpath,/usr/local/boost-1_71_0-py374/lib  
*/  
```  
```  
% gdb /usr/local/python374/bin/python3.7dm  
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-115.el7  
Copyright (C) 2013 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"  
and "show warranty" for details.  
This GDB was configured as "x86_64-redhat-linux-gnu".  
For bug reporting instructions, please see:  
<http://www.gnu.org/software/gdb/bugs/>...  
Reading symbols from /usr/local/python374/bin/python3.7dm...done.  
(gdb) run  
Starting program: /usr/local/python374/bin/python3.7dm   
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib64/libthread_db.so.1".  
Python 3.7.4 (default, Oct  7 2019, 08:57:43)   
[GCC 4.8.5 20150623 (Red Hat 4.8.5-39)] on linux  
Type "help", "copyright", "credits" or "license" for more information.  
>>> import teststr3  
>>> exit()  
  
Program received signal SIGSEGV, Segmentation fault.  
0x000000000044602a in list_dealloc (op=op@entry=0x7ffff7f4b360)  
    at ../Objects/listobject.c:316  
316	    Py_TRASHCAN_SAFE_BEGIN(op)  
Missing separate debuginfos, use: debuginfo-install libgcc-4.8.5-39.el7.x86_64 libstdc++-4.8.5-39.el7.x86_64  
(gdb) where  
#0  0x000000000044602a in list_dealloc (op=op@entry=0x7ffff7f4b360)  
    at ../Objects/listobject.c:316  
#1  0x0000000000466452 in _Py_Dealloc (op=0x7ffff7f4b360)  
    at ../Objects/object.c:1971  
#2  0x00007ffff05151f3 in boost::python::api::object_base::~object_base (  
    this=0x7ffff071ed00 <teststr3::aaa_list>, __in_chrg=<optimized out>)  
    at /usr/local/boost-1_71_0-py374/include/boost/python/object_core.hpp:423  
#3  0x00007ffff0515088 in boost::python::api::object::~object (  
    this=0x7ffff071ed00 <teststr3::aaa_list>, __in_chrg=<optimized out>)  
    at /usr/local/boost-1_71_0-py374/include/boost/python/object_core.hpp:238  
#4  0x00007ffff0515290 in boost::python::detail::list_base::~list_base (  
    this=0x7ffff071ed00 <teststr3::aaa_list>, __in_chrg=<optimized out>)  
    at /usr/local/boost-1_71_0-py374/include/boost/python/list.hpp:18  
#5  0x00007ffff051536a in boost::python::list::~list (  
    this=0x7ffff071ed00 <teststr3::aaa_list>, __in_chrg=<optimized out>)  
    at /usr/local/boost-1_71_0-py374/include/boost/python/list.hpp:57  
#6  0x00007ffff6eeac99 in __run_exit_handlers (status=status@entry=0,   
    listp=0x7ffff72786c8 <__exit_funcs>,   
    run_list_atexit=run_list_atexit@entry=true) at exit.c:77  
#7  0x00007ffff6eeace7 in __GI_exit (status=status@entry=0) at exit.c:99  
#8  0x0000000000517904 in Py_Exit (sts=sts@entry=0)  
    at ../Python/pylifecycle.c:2292  
#9  0x000000000051fb14 in handle_system_exit () at ../Python/pythonrun.c:636  
---Type <return> to continue, or q <return> to quit---  
#10 0x0000000000520da8 in PyErr_PrintEx (  
    set_sys_last_vars=set_sys_last_vars@entry=1) at ../Python/pythonrun.c:646  
#11 0x000000000052117f in PyErr_Print () at ../Python/pythonrun.c:542  
#12 0x0000000000521d3a in PyRun_InteractiveLoopFlags (  
    fp=fp@entry=0x7ffff7279640 <_IO_2_1_stdin_>,   
    filename_str=filename_str@entry=0x5e55b2 "<stdin>",   
    flags=flags@entry=0x7fffffffe2d0) at ../Python/pythonrun.c:134  
#13 0x00000000005223f8 in PyRun_AnyFileExFlags (  
    fp=fp@entry=0x7ffff7279640 <_IO_2_1_stdin_>, filename=0x5e55b2 "<stdin>",   
    closeit=closeit@entry=0, flags=flags@entry=0x7fffffffe2d0)  
    at ../Python/pythonrun.c:78  
#14 0x0000000000422023 in pymain_run_file (fp=0x7ffff7279640 <_IO_2_1_stdin_>,   
    filename=0x0, p_cf=p_cf@entry=0x7fffffffe2d0) at ../Modules/main.c:427  
#15 0x00000000004220e5 in pymain_run_filename (  
    pymain=pymain@entry=0x7fffffffe300, cf=cf@entry=0x7fffffffe2d0)  
    at ../Modules/main.c:1606  
#16 0x00000000004221d8 in pymain_run_python (pymain=pymain@entry=0x7fffffffe300)  
    at ../Modules/main.c:2867  
#17 0x000000000042550f in pymain_main (pymain=pymain@entry=0x7fffffffe300)  
    at ../Modules/main.c:3028  
#18 0x0000000000425589 in _Py_UnixMain (argc=<optimized out>,   
    argv=<optimized out>) at ../Modules/main.c:3063  
#19 0x000000000041f116 in main (argc=<optimized out>, argv=<optimized out>)  
---Type <return> to continue, or q <return> to quit---  
    at ../Programs/python.c:15  
(gdb)   
```

---

## Comment 3

> Username: ysnt27  
> Created at: 2019-10-25 08:37:13 UTC  
> Url: https://github.com/boostorg/python/issues/248#issuecomment-546260303  

With python-3.7.5(debug build) and GDB, I tried step execution.  
  
Python-3.7.5/Objects/listobject.c  
  
``` cpp  
static void  
list_dealloc(PyListObject *op)  
{  
    Py_ssize_t i;  
    PyObject_GC_UnTrack(op);  
    Py_TRASHCAN_SAFE_BEGIN(op) // <--- SIGSEGV here!!  
```  
  
The macro definition is  
  
``` cpp  
#define Py_TRASHCAN_SAFE_BEGIN(op) \  
    do { \  
        PyThreadState *_tstate = PyThreadState_GET(); \  
        if (_tstate->trash_delete_nesting < PyTrash_UNWIND_LEVEL) { \  
            ++_tstate->trash_delete_nesting;  
            /* The body of the deallocator is here. */  
```  
  
`_tstate` was NULL.  
  
So, `_tstate->trash_delete_nesting` resulted in segmentation fault.

---

## Comment 4

> Username: ycollet  
> Created at: 2019-10-25 09:16:14 UTC  
> Url: https://github.com/boostorg/python/issues/248#issuecomment-546274668  

A patch on python which has worked for me on Python-3.6.7 (works also for other versions, but you need to adapt it):  
```  
$ diff dictobject.c-3.6.7 /tmp/Python-3.6.7/Objects/dictobject.c   
2009c2009  
<     Py_ssize_t i, n, refcnt = mp->ob_base.ob_refcnt;  
---  
>     Py_ssize_t i, n;  
2013,2015c2013  
<     if (refcnt != 0) {  
<       Py_TRASHCAN_SAFE_BEGIN(mp)  
<     }  
---  
>     Py_TRASHCAN_SAFE_BEGIN(mp)  
2033,2035c2031  
<     if (refcnt != 0) {  
<       Py_TRASHCAN_SAFE_END(mp)  
<     }  
---  
>     Py_TRASHCAN_SAFE_END(mp)  
```  
This is a workaround and not a fix ...

---

## Comment 5

> Username: ysnt27  
> Created at: 2019-10-25 09:48:11 UTC  
> Url: https://github.com/boostorg/python/issues/248#issuecomment-546285831  

If possible, I need a workaround at the side of boost.python, not Python.  
  
Though,  Python's issue, https://bugs.python.org/issue17703  
  seems related.

---

## Comment 6

> Username: ysnt27  
> Created at: 2019-10-28 02:50:14 UTC  
> Url: https://github.com/boostorg/python/issues/248#issuecomment-546770998  

I opened an issue at Python Bug Tracker,  
https://bugs.python.org/issue38609
