# #166 Fix memory leaks in enum.cpp [Merged]

> Username: elmopl  
> Created at: 2017-10-24 10:09:40 UTC  
> Updated at: 2017-10-25 12:04:37 UTC  
> Merged at: 2017-10-25 12:04:36 UTC  
> Closed at: 2017-10-25 12:04:36 UTC  
> Url: https://github.com/boostorg/python/pull/166  

Unfortunately due to optimised build of Python3 libraries and executable I got only partial stack from [http://clang.llvm.org/docs/AddressSanitizer.html], however digging into and reducing my code I tracked it down to be issue with `boost/libs/python/src/object/enum.cpp`.  
  
It has two bits that leak (and comment mentioning there is one):  
  
    PyObject *mod = PyObject_GetAttrString( self_, "__module__");  
  
Leaks reference, as it never decreases it.  
It also stores a new string object under object's `name` that ref count never gets decremented.  
  
That commit fixes both issues.

---

## Comment 1

> Username: elmopl  
> Created_at: 2017-10-24 10:19:40 UTC  
> Updated_at: 2017-10-24 10:20:07 UTC  
> Url: https://github.com/boostorg/python/pull/166#issuecomment-338944258  

As this is my first attempt to push something to boos, can someone with more experience tell me if that is usual for those automatic builds to fail?  
  
To me error seems entirely unrelated to my change:  
  
```  
   Creating library bin.SCons\msvc-14.0\release\dynamic\threading-multi\test\args_ext.lib and object bin.SCons\msvc-14.0\release\dynamic\threading-multi\test\args_ext.exp  
    args.obj : error LNK2019: unresolved external symbol "struct test_class<0> const volatile * __cdecl boost::get_pointer<struct test_class<0> const volatile >(struct test_class<0> const volatile *)" (??$get_pointer@$$CDU?$test_class@$0A@@@@boost@@YAPDU?$test_class@$0A@@@PDU1@@Z) referenced in function "private: static struct _typeobject * __cdecl boost::python::objects::make_ptr_instance<struct test_class<0>,struct boost::python::objects::pointer_holder<struct test_class<0> *,struct test_class<0> > >::get_derived_class_object<struct test_class<0> >(struct boost::mpl::bool_<1>,struct test_class<0> const volatile *)" (??$get_derived_class_object@U?$test_class@$0A@@@@?$make_ptr_instance@U?$test_class@$0A@@@U?$pointer_holder@PAU?$test_class@$0A@@@U1@@objects@python@boost@@@objects@python@boost@@CAPAU_typeobject@@U?$bool_@$00@mpl@3@PDU?$test_class@$0A@@@@Z)  
```  
  
Have I actually caused that failure with my changes?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2017-10-24 12:19:28 UTC  
> Url: https://github.com/boostorg/python/pull/166#issuecomment-338971027  

Hi Krzysztof,  
thanks for your patch. I'll review it as soon as possible.  
Don't worry about the build failure. It is, as you suspect, entirely unrelated indeed.

---

## Comment 3

> Username: elmopl  
> Created_at: 2017-10-24 15:53:27 UTC  
> Url: https://github.com/boostorg/python/pull/166#issuecomment-339038735  

If that helps I have quite a lot of modules built using `boost::python` that comes from library that was built with above patch.  
  
 - Linux (with ASAN enabled on all code, except for the core Python itself, running with `PYTHONMALLOC=malloc` set)  
 - Solaris (just a "plain" setup, don't have tools for memory checking on that platform)  
 - AIX (same as above)  
  
As far as I can see patching like above had no visible effect on the last two (having said which, having leaks also had no visible effects, all tests passed then as they do now).  
It allowed me to remove one of the rules from memory suppression list for Linux runs.  
  
So as far as my testing goes it seems to work fine (and I know at least some things got picked up by ASAN because at first I did not call `Py_TYPE(self)->tp_free((PyObject*)self);` which caused quite nasty crashes).

---

## Review 4 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-10-24 15:58:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/166#pullrequestreview-71575537  

📁 src/object/enum.cpp

```diff
  40 |-         // const char *mod = PyString_AsString(PyObject_GetAttrString( self_, const_cast<char*>("__module__")));
  46 |         PyObject *mod = PyObject_GetAttrString( self_, "__module__");
  47 |+         object auto_free(handle<>(mod));
```

> Username: stefanseefeld  
> Created_at: 2017-10-24 15:58:00 UTC  
> Url: https://github.com/boostorg/python/pull/166#discussion_r146609200  

A little nit-pick: with `auto_free` taking ownership of `mod`, I think it would be cleaner to fuse those two lines together and eliminate the `mod` variable alltogether.


---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2017-10-24 16:00:44 UTC  
> Url: https://github.com/boostorg/python/pull/166#issuecomment-339041245  

Thanks for the additional info. Would you mind sharing a little more about your memory checking setup ? Is that something I could reproduce myself, or even turn into CI-driven tests ?

---

## Comment 6

> Username: elmopl  
> Created_at: 2017-10-24 16:18:25 UTC  
> Url: https://github.com/boostorg/python/pull/166#issuecomment-339046917  

All code on Linux is compiled with `g++` ("g++ (GCC) 6.2.1 20160916 (Red Hat 6.2.1-3)")  with `-fno-omit-frame-pointer -fsanitize=address` command line arguments and `-lsan` for linking (that includes also build for `libboost_python.so`).  
This simply adds some extra memory checks, on of which is checking for memory leaks (it simply prints report when process exits if something leaked).  
  
I would think it should be as simple as adding those arguments to `g++` (or `clang`, I am pretty sure `clang` has it also) and then it will simply exit tests if it hits cases that leaked or did other detected memory violation.

---
