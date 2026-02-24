# #291 Use python macros to permit alternate pythons (start pypy) [Merged]

> Username: jcpunk  
> Created at: 2020-03-02 14:32:02 UTC  
> Updated at: 2020-10-16 16:35:55 UTC  
> Merged at: 2020-10-16 16:35:44 UTC  
> Closed at: 2020-10-16 16:35:45 UTC  
> Url: https://github.com/boostorg/python/pull/291  

Resolves https://svn.boost.org/trac10/ticket/4125  
  
This change uses the official python macros for object access.  This should have no impact on existing CPython usage, but will permit alternate pythons (such as pypy) to utilize boost.

---

## Comment 1

> Username: mattip  
> Created_at: 2020-06-25 05:57:11 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-649236688  

While we are at it, could you replace the use of the CPython private `_Py_fopen` with the standard `fopen`?

---

## Comment 2

> Username: jcpunk  
> Created_at: 2020-06-29 15:28:52 UTC  
> Updated_at: 2020-06-29 15:59:36 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-651194318  

I'm not sure how that would interact with Unicode translations....  I'm worried (perhaps unnecessarily) that just aliasing `_Py_fopen` to `fopen` on PyPy might hide some sort of character encoding translation bug type thing.  I know almost nothing on the character encoding front....  
  
Looking through the commit history, I'm trying to understand the rational between https://github.com/boostorg/python/blob/develop/src/exec.cpp#L108 and https://github.com/boostorg/python/blob/develop/src/exec.cpp#L110.  
  
I suppose I've a few questions on that front:  
  
- What is the oldest version of CPython (2 and 3) this code should support?  
- What is the oldest version of PyPy we would want to support?  
  
I've taken a stab at maintaining compatibility with all version of Python 3 and adding support for PyPy (which doesn't have a Python 3.0 branch so the Private API could stay in 3.0).

---

## Comment 3

> Username: mattip  
> Created_at: 2020-06-30 05:03:05 UTC  
> Updated_at: 2020-06-30 07:22:51 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-651537083  

The CI run I looked at failed with   
```  
Error: /usr/local/lib/python2.7/dist-packages/faber/tools/python.py:98: \  
'python' object has no attribute 'lib'  
```  
  
Edit: on travis

---

## Comment 4

> Username: mattip  
> Created_at: 2020-06-30 07:22:26 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-651600434  

I think [this line](https://github.com/boostorg/python/blob/develop/.appveyor.yml#L67) in appveyor' setup must be changed to `python -m pip install ...`

---

## Comment 5

> Username: jcpunk  
> Created_at: 2020-06-30 12:58:37 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-651773967  

The AppVeyor bits seem to have a PR https://github.com/boostorg/python/pull/294

---

## Comment 6

> Username: jcpunk  
> Created_at: 2020-10-13 18:37:54 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-707933360  

Looks like Python 3.10 will complain about the non-public symbols.  
  
I've reworked the patches a bit to make pypy optional in Travis CI for now.

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2020-10-13 19:24:19 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-707957610  

Hi there, sorry for the high latency. I have no experience with PyPy, and thus will require a bit of help in adding support for it to `boost.python`. Given that I'm using `faber` to do the build, I'll start by adding the required `PyPy` support there (https://github.com/stefanseefeld/faber/issues/21).

---

## Comment 8

> Username: jcpunk  
> Created_at: 2020-10-13 20:16:35 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-707983339  

Sounds good!  
  
Commits 1 and 2 from this branch will probably be required for cPython 3.10 final.

---

## Review 9 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-10-15 16:33:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/291#pullrequestreview-509565451  

📁 .travis.yml

```diff
  32 |+       env: CXX=g++ PYTHON=pypy CXXFLAGS=-std=c++11
  33 |+     - os: linux
  34 |+       env: CXX=clang++ PYTHON=pypy CXXFLAGS=-std=c++98
```

> Username: stefanseefeld  
> Created_at: 2020-10-15 16:33:27 UTC  
> Updated_at: 2020-10-16 15:09:52 UTC  
> Url: https://github.com/boostorg/python/pull/291#discussion_r505682388  

I'm not convinced such extensive coverage is needed. I'd only add two new build variants to cover pypy and pypy3 with C++11.

> Username: jcpunk  
> Created_at: 2020-10-15 17:04:54 UTC  
> Updated_at: 2020-10-16 15:09:52 UTC  
> Url: https://github.com/boostorg/python/pull/291#discussion_r505702031  

I'll drop those and re-push.


---

## Review 10 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-10-15 16:33:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/291#pullrequestreview-509565636  

📁 .travis.yml

```diff
  43 |     - os: osx
  44 | 
  45 |+ 
```

> Username: stefanseefeld  
> Created_at: 2020-10-15 16:33:41 UTC  
> Updated_at: 2020-10-16 15:09:52 UTC  
> Url: https://github.com/boostorg/python/pull/291#discussion_r505682502  

extra newline


---

## Comment 11

> Username: jcpunk  
> Created_at: 2020-10-15 18:37:06 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-709516332  

In theory I've addressed your requests and Travis will validate that shortly..... in practice.... well we'll see....

---

## Comment 12

> Username: stefanseefeld  
> Created_at: 2020-10-15 19:40:27 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-709549057  

Thanks, the PR looks good. I'll approve once the builds complete successfully.

---

## Comment 13

> Username: stefanseefeld  
> Created_at: 2020-10-15 22:37:05 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-709626288  

I do see a bunch of test failures, but suspect that they are at least partially caused by my recent changes to `Faber`. I'll investigate.

---

## Comment 14

> Username: stefanseefeld  
> Created_at: 2020-10-16 01:19:18 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-709672868  

OK, so I have fixed the `Faber` tool's failures. Now the only failures are with the newly added `PyPy` backends.

---

## Comment 15

> Username: jcpunk  
> Created_at: 2020-10-16 14:21:20 UTC  
> Updated_at: 2020-10-16 14:23:27 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710077607  

The remaining errors generated on the pypy compile seem to be:  
  
```  
include/boost/python/detail/is_auto_ptr.hpp:17:40: warning: ‘template<class> class std::auto_ptr’ is deprecated  
  
 BOOST_PYTHON_IS_XXX_DEF(auto_ptr, std::auto_ptr, 1)  
                                        ^  
```  
  
It looks like C++11 added `unique_ptr` to use instead, but that isn't available in older C++ versions.[1]  
  
I'm pretty sure I'm not qualified to assess the risks in modifying this..... I think boost provides `shared_ptr`[2] for this use case, but I'm well out of my depth here...  
  
  
[1]  
https://en.wikipedia.org/wiki/Auto_ptr  
https://en.wikipedia.org/wiki/Smart_pointer#unique_ptr  
  
  
[2]  
https://www.boost.org/doc/libs/1_61_0/libs/smart_ptr/shared_ptr.htm

---

## Comment 16

> Username: stefanseefeld  
> Created_at: 2020-10-16 14:44:09 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710090177  

I don't think `std::auto_ptr` is causing the failures. The above is just a warning, and it's present with the CPython variants as well. (You can disable it by adding `cxxflags=-W-no-deprecated-warnings`)

---

## Comment 17

> Username: stefanseefeld  
> Created_at: 2020-10-16 14:48:56 UTC  
> Updated_at: 2020-10-16 14:53:35 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710092859  

There is, however, this error:  
```  
./src/object/function.cpp: In static member function ‘static void boost::python::objects::function::add_to_namespace(const boost::python::api::object&, const char*, const boost::python::api::object&, const char*)’:  
./src/object/function.cpp:436:60: error: ‘struct PyClassObject’ has no member named ‘cl_dict’  
             dict = handle<>(borrowed(((PyClassObject*)ns)->cl_dict));  
```  
(in the `pypy` variant).

---

## Comment 18

> Username: stefanseefeld  
> Created_at: 2020-10-16 14:54:01 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710095705  

...and the `pypy3` variant has a bunch of test failures with  
```  
RPython traceback:  
  File "pypy_module_cpyext.c", line 57899, in wrapper_second_level__star_2_36  
  File "pypy_module_cpyext_1.c", line 57139, in PyType_IsSubtype  
  File "pypy_module_cpyext_1.c", line 35912, in from_ref  
Fatal RPython error: AssertionError  
Aborted (core dumped)  
```

---

## Comment 19

> Username: jcpunk  
> Created_at: 2020-10-16 15:10:20 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710105151  

With python 2 being EOL, I'm thinking we don't need to support `pypy2`.  I'm not sure which users would want a "new boost" but an old pypy.  
  
I'll have to look into the `pypy3` failures more closely.  It looks like a the reference counting bits may need some review...  
  
For now I've flipped `pypy3` into `allowed failure` as commits 1 and 2 from here are pre-reqs for pypy and look like they'll be required for python 3.10.

---

## Comment 20

> Username: stefanseefeld  
> Created_at: 2020-10-16 15:13:13 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710106706  

OK, fair enough.

---

## Comment 21

> Username: jcpunk  
> Created_at: 2020-10-16 16:24:46 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710175021  

With the builds passing for CPython here, I'd say this part is complete.  The `pypy3` errors look like they'll require some more invasive work.

---

## Comment 22

> Username: stefanseefeld  
> Created_at: 2020-10-16 16:35:55 UTC  
> Url: https://github.com/boostorg/python/pull/291#issuecomment-710190355  

Thanks for the PR !

---
