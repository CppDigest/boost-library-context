# #376 - boost::python::numpy::initialize() incompatibilities with numpy>=1.21 [Closed]

> Username: jawsc-alan  
> Created at: 2021-11-05 01:01:56 UTC  
> Updated at: 2024-07-16 19:31:13 UTC  
> Closed at: 2024-07-16 18:15:13 UTC  
> Url: https://github.com/boostorg/python/issues/376  

## Summary  
  
The following anomaly applies to boost::python operating with NumPy version >= 1.21.0; prior to this version the degradation doesn't occur:  
  
### Symptom  
If a `BOOST_PYTHON_MODULE` calls `boost::python::numpy::initialize()`, boost::python's parameter type detection feature is partially-defeated.  This affects any method or function type checking and is particularly noticeable in overloads.  If an incorrect parameter type (such as an object) is  
passed to a method/function that expects an intrinsic int, float, or string, a segfault occurs instead of  
the helpful boost error throw.  In the event of an overload, the overload-dispatcher may send a legitimate parameter to the incorrect overload instance.  
  
These cases are exercised in Python example 1 and Python example 2 below, respectively.  
  
## Relevant Software Versions Information:  
### For Failure:  
(several were tried, this is representative)  
  
| package | Version | Code |  Source |  
| ------- | ------- | ---- | ------- |  
| boost  |  1.72.0    |  py39hb64e6f8_1  |  conda-forge|  
| python        |            3.9.7        |        h88f2d9e_1 | |  
| numpy          |           1.21.2      |     py39h0fa1045_0  | |  
| clang | Apple clang version 11.0.0 (clang-1100.0.33.17) | | |  
  
  
### For Success(expected behavior):   
just change numpy:  
  
| package | Version | Code |  Source |  
| ------- | ------- | ---- | ------- |  
| numpy         |            1.20.3      |     py39h7eed0ac_1  |  conda-forge |  
  
## C++ sample:  
  
minipy.cpp  
```c   
#include <boost/python.hpp>  
#include <boost/python/extract.hpp>  
#include <boost/python/numpy.hpp>  
#include <string>  
#include <sstream>  
#include <vector>  
  
using namespace std;  
using namespace boost::python;  
namespace np = boost::python::numpy;  
  
class A {  
public:  
  A():sum(0.0) {}  
  
  void add(double d) {sum += d;}  
  void add(const A& s) {sum += s.sum;}  
  
  void mul(double d) {sum *= d;}  
  double val() const { return sum;}  
private:  
  double sum;  
};  
  
BOOST_PYTHON_MODULE(minipy)  
{  
  Py_Initialize();  
  if(0) np::initialize(false);  
  else np::initialize();  
  
  class_<A>("A", init<>())  
  .def("add", static_cast<void (A::*) (const A&)>(&A::add))  
  .def("add", static_cast<void (A::*) (double)>(&A::add))  
  .def("mul", &A::mul)  
  .def("__call__", &A::val)  
  ;  
};  
  
```  
Build with standard boost libraries/paths.  in this case for me, conda venv = compile2:  
```  
g++ -I/Users/Alan/miniconda3/envs/compile2/include/python3.9 -I/Users/Alan/miniconda3/envs/compile2/include/python3.9 -I/Users/Alan/miniconda3/envs/compile2/include -O3 -fPIC -std=c++11  -Iinclude   -MMD -c -o objs/minipy.o src/minipy.cpp  
  
g++ -Wl,-rpath,/Users/Alan/miniconda3/envs/compile2/lib -shared -o lib/minipy.so objs/minipy.o -L/Users/Alan/miniconda3/envs/compile2/lib -Llib -ldl -framework CoreFoundation -undefined dynamic_lookup -lboost_python39 -lboost_numpy39 -larmadillo   
  
```  
  
**Observations**:  
* Parameters: A.add() can be passed a double or another A.  A.mul() expects a double.  
* I added an if(0) (line 28/29 of the source) to turn register_scalar_converters on or off  
  
## Python example 1:  
(This is fast to reproduce, so a command line python may be simpler)  
  
```python  
import minipy as m  
  
a = m.A()  
a.add(a)  # Segmentation fault: 11 if nump>1.21; register_scalar_converters is true  
```  
  
### Expected output:  
  
```  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
Boost.Python.ArgumentError: Python argument types in  
    A.mul(A, A)  
did not match C++ signature:  
    mul(A {lvalue}, double)  
  
```  
### Incorrect output:  
  
`Segmentation fault: 11`  
  
## Python example 2 -- boost overloading failure:  
  
```python  
import minipy as m  
  
a = m.A()  
a.add(a)  
  
print(a())  
```  
  
### Expected output:  
  
```  
0.0  
```  
  
### Incorrect output:  
  
```  
Segmentation fault: 11  
```  
  
**Observation**: It appears from this example that the numpy registration in numpy >= 1.21  
is defeating boost::python's type matching for overloads.  If the overloads are declared  
in the opposite order, the code works regardless of the numpy version.  Given the last-in-first-checked order   
of boost::python dispatcher, add(double) is checked first in the code example above.  Normally this is fine, but if numpy>=1.21 registration has occurred the  
m.A object is incorrectly considered convertible to double, then leading to the segmentation fault.

---

## Comment 1

> Username: dwpaley  
> Created at: 2021-11-30 23:49:11 UTC  
> Updated at: 2021-12-01 01:01:07 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-983131024  

This is connected to changes in numpy type casting implemented in these two numpy PRs: https://github.com/numpy/numpy/pull/17401 and https://github.com/numpy/numpy/pull/18676.   
  
For hacking around with the Numpy sources the following Dockerfile might be convenient:  
<details> <summary>Dockerfile</summary>  
  
```  
FROM centos:7  
RUN mkdir /mc3 && \  
  cd /mc3 && \  
  curl -LO https://repo.anaconda.com/miniconda/Miniconda3-py37_4.10.3-Linux-x86_64.sh && \  
  /bin/sh Miniconda3-*.sh -b -u -p /mc3  
RUN source /mc3/etc/profile.d/conda.sh && \  
  conda create -n build python=3.9 c-compiler cxx-compiler cython -c conda-forge -y && \  
  conda create -n test python=3.9 numpy  
  
RUN yum install git -y  
  
RUN mkdir /work && \  
  cd /work && \  
  mkdir lib objs src  
  
COPY minipy.cpp /work/src  
COPY repro.py /work/lib  
  
RUN source /mc3/etc/profile.d/conda.sh && \  
  conda activate build && \  
  conda install boost -c conda-forge -y  
  
RUN source /mc3/etc/profile.d/conda.sh && \  
  conda activate build && \  
  cd /work && \  
  g++ -I /mc3/envs/build/include/python3.9 -I /mc3/envs/build/include/ -O3 -fPIC -std=c++11  -Iinclude   -MMD -c -o objs/minipy.o src/minipy.cpp && \  
  g++ -Wl,-rpath,/mc3/envs/build/lib -shared -o lib/minipy.so objs/minipy.o -L/mc3/envs/build/lib -Llib -ldl -lboost_python39 -lboost_numpy39  
  
RUN mkdir /numpy && \  
  cd /numpy && \  
  git clone https://github.com/numpy/numpy  
  
RUN source /mc3/etc/profile.d/conda.sh && \  
  conda create -n build2 python=3.9 c-compiler cxx-compiler cython -c conda-forge -y && \  
  conda activate build2 && \  
  cd /numpy/numpy && \  
  git submodule update --init && \  
  python setup.py build_ext -i && \  
  cd /mc3/envs/test/lib/python3.9/site-packages && \  
  mv numpy numpy.nerf && \  
  ln -s /numpy/numpy/numpy numpy  
```  
</details>  
where `minipy.cpp` is as given by @jawsc-alan and `repro.py` is either of the Python snippets above. Then in an interactive container, rebuilding numpy is done by:  
  
```  
$ source /mc3/etc/profile.d/conda.sh  
$ cd /numpy/numpy  
$ conda activate build2  
$ NPY_USE_NEW_CASTINGIMPL=1 python setup.py build_ext -i  
```  
After numpy commit ba77419b10, this procedure gives the segfault described here; then after e108c76 the `NPY_USE_NEW_CASTINGIMPL` variable is not needed to reproduce it because the new casting became the only option.  
  
Not sure where to take it from here, but I hope this is helpful...

---

## Comment 2

> Username: dwpaley  
> Created at: 2021-12-02 19:34:16 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-984939357  

Made some progress on this. First things first, this Dockerfile will provide debug symbols for numpy, boost_python, python, etc:  
<details> <summary>Dockerfile</summary>  
  
```  
FROM fedora:35  
  
RUN dnf install -y dnf-plugins-core which git findutils gdb valgrind gcc g++  
RUN dnf install -y glibc-devel libffi-devel libstdc++-devel openssl-devel \  
  zlib-devel  
RUN dnf install -y python3-devel cython  
RUN dnf debuginfo-install -y python3 glibc libffi libgcc libstdc++ openssl zlib  
  
RUN dnf install -y bzip2  
RUN mkdir /boost && \  
  cd /boost && \  
  curl -LO https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.bz2 && \  
  bunzip2 boost_1_77_0.tar.bz2 && \  
  tar -xf boost_1_77_0.tar  
  
RUN ln -s `which python3` /usr/bin/python  
RUN pip3 install numpy  
RUN cd /boost/boost_1_77_0/tools/build && \  
  ./bootstrap.sh && \  
  ./b2 install --prefix=/boost/b2 && \  
  cd /boost/boost_1_77_0 && \  
  /boost/b2/bin/b2 --with-python --prefix=/boost_build -d0 variant=debug install  
  
  
RUN dnf debuginfo-install -y cython  
RUN mkdir /numpy && \  
  cd /numpy && \  
  git clone https://github.com/numpy/numpy && \  
  cd numpy && \  
  git submodule update --init && \  
  CFLAGS="-g" CPPFLAGS="-g" python setup.py build_ext -i  
  
RUN mkdir /work && \  
  cd /work && \  
  mkdir lib objs src  
  
COPY minipy.cpp /work/src  
COPY repro.py /work/lib  
  
RUN cd /work && \  
  g++ -g -I/boost_build/include/ -I/usr/include/python3.10 -fPIC -std=c++11  -Iinclude   -MMD -c -o objs/minipy.o src/minipy.cpp && \  
  g++ -g -Wl,-rpath,/boost_build/lib -shared -o lib/minipy.so objs/minipy.o -L/boost_build/lib -Llib -ldl -lboost_python310 -lboost_numpy310  
  
  
RUN mv /usr/local/lib64/python3.10/site-packages/numpy /usr/local/lib64/python3.10/site-packages/numpy.nerf && \  
  ln -s /numpy/numpy/numpy /usr/local/lib64/python3.10/site-packages/numpy  
```  
</details>  
  
Here is the segfault:  
  
```  
[root@701ff080ecdc lib]# gdb --args python repro.py  
(gdb) run  
Starting program: /usr/bin/python repro.py  
Program received signal SIGSEGV, Segmentation fault.  
PyArray_GetCastingImpl (from=0x7f4cdff00d20 <boost::python::class_metatype_object>, to=to@entry=0x559ccd752120) at numpy/core/src/multiarray/convert_datatype.c:77  
77	        res = PyDict_GetItemWithError(NPY_DT_SLOTS(from)->castingimpls, (PyObject *)to);  
```  
  
This is an excerpt from the function body:  
  
```  
  
/**  
 * Fetch the casting implementation from one DType to another.  
 *  
 * @params from  
 * @params to  
 *  
 * @returns A castingimpl (PyArrayDTypeMethod *), None or NULL with an  
 *          error set.  
 */  
NPY_NO_EXPORT PyObject *  
PyArray_GetCastingImpl(PyArray_DTypeMeta *from, PyArray_DTypeMeta *to)  
{  
    PyObject *res;  
    if (from == to) {  
        res = NPY_DT_SLOTS(from)->within_dtype_castingimpl;  
    }  
    else {  
        res = PyDict_GetItemWithError(NPY_DT_SLOTS(from)->castingimpls, (PyObject *)to);  
    }  
    if (res != NULL || PyErr_Occurred()) {  
        Py_XINCREF(res);  
        return res;  
    }  
```  
  
In my understanding `from` and `to` are data types that have been implicitly cast to `PyArray_DTypeMeta` here. The macro `NPY_DT_SLOTS` gets expanded to this: `#define NPY_DT_SLOTS(dtype) ((NPY_DType_Slots *)(dtype)->dt_slots)`. It looks like the new-style numpy dtypes all have that data member `dt_slots` but boost_python types don't. So this is the expected behavior of `from` and `to`:  
```  
(gdb) p *((NPY_DType_Slots *)(to)->dt_slots)  
$16 = {  
  discover_descr_from_pyobject = 0x7faf0f412c80 <nonparametric_discover_descr_from_pyobject>,  
  is_known_scalar_type = 0x7faf0f412f60 <python_builtins_are_known_scalar_types>,  
  default_descr = 0x7faf0f412c90 <nonparametric_default_descr>,  
  common_dtype = 0x7faf0f4132a0 <default_builtin_common_dtype>,  
  common_instance = 0x0, setitem = 0x0, getitem = 0x0,  
  within_dtype_castingimpl = <numpy._ArrayMethod at remote 0x7faf0f1ff030>,  
  castingimpls = {<numpy._DTypeMeta at remote 0x56020c63ab00>: <numpy._ArrayMethod at etc etc etc  
```  
  
and this is what we get instead when `from` is a boost class:  
```  
(gdb) p *((NPY_DType_Slots *)(from)->dt_slots)  
Cannot access memory at address 0x0  
```  
  
So it really looks like a numpy bug in the new type casting implementation. I'm not sure exactly what should be the correct behavior, it appears there's a legacy system that has been preserved so maybe that is still usable for types that don't have a data member `dt_slots`. Will reflect on this more but maybe opening a numpy issue is the right path forward.

---

## Comment 3

> Username: dwpaley  
> Created at: 2021-12-02 22:15:20 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-985045215  

This fixes it! Will open a numpy PR.  
  
```  
diff --git a/numpy/core/src/multiarray/convert_datatype.c b/numpy/core/src/multiarray/convert_datatype.c  
index 3135d6989..8c8e2c2aa 100644  
--- a/numpy/core/src/multiarray/convert_datatype.c  
+++ b/numpy/core/src/multiarray/convert_datatype.c  
@@ -73,6 +73,9 @@ PyArray_GetCastingImpl(PyArray_DTypeMeta *from, PyArray_DTypeMeta *to)  
     if (from == to) {  
         res = NPY_DT_SLOTS(from)->within_dtype_castingimpl;  
     }  
+    else if (NPY_DT_SLOTS(from) == NULL) {  
+        res = NULL;  
+    }  
     else {  
         res = PyDict_GetItemWithError(NPY_DT_SLOTS(from)->castingimpls, (PyObject *)to);  
     }  
```  
  
If an urgent fix is needed it's possible to clone numpy sources, apply this diff, rebuild numpy and symlink the result inside e.g. `conda_base/lib/python3.8/site-packages/` or equivalent.

---

## Comment 4

> Username: jakirkham  
> Created at: 2021-12-03 00:43:25 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-985118167  

cc @rgommers (for awareness)

---

## Comment 5

> Username: seberg  
> Created at: 2021-12-03 16:10:09 UTC  
> Updated at: 2021-12-03 16:10:40 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-985643409  

Maybe floating it here is more helpful:  The fix above seems like a bad idea to me, it is a small fix to cover up some of the symptoms of a much larger problem.  
  
I am still struggling to have a good local reproducer, +but the problem comes from here:  
https://github.com/boostorg/python/blob/f5d14ef15e98838b3cf18692d46c481287ed50d0/src/numpy/dtype.cpp#L161-L162  
  
The second line seems OK (if shady).  The first line gets some `PyArray_Descr *` that is "faulty" and probably created by `boost::python`?  To understand deeper I really need to understand what exactly that object is and how /where it is created... And right now I am not even sure where to look.  (Sorry, my C++ is not on a high enough level to understand boost-level code well.)  
  
NB: The `equivalent` seems a bit shady because EquivTypes does not really do what it is used for here.  But in practice that is probably not a problem.  However, unless you are interacting with weird user defined dtypes that do shady things, there should be better ways to do this.

---

## Comment 6

> Username: seberg  
> Created at: 2021-12-03 16:18:25 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-985649473  

To be fair, it seems likely that whatever that `dt` is there, the only way it is _ever_ used is in `PyArray_EquivTypes`.  So adding this type of hack into `PyArray_EquivTypes` (for a few releases) may well be the pragmatic thing to do.  
  
But I would still like to understand what triggers this problem and clarify that whatever triggers means that this is an invalid `dtype`.

---

## Comment 7

> Username: seberg  
> Created at: 2021-12-13 15:42:29 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-992605719  

Just to see if I can get this moving here:  
* `equivalent` should be completely useles (even the windows-only path seems wrong).  It seems to think that the scalar type is the same as the dtype, which is wrong.  
* Calling `PyArray_EquivTypes` with Python types is incorrect API and ABI (which always returned undefined results, that will have been always False though, since a type object is much larger than a NumPy dtype, it would not have crashed)  
  
The fix is to just delete the `equivalent` path (after double checking that it does nothing in windows as well maybe).  If you want to fix it, the windows path does `sizeof(int) == sizeof(long)`.  Add the same for `sizeof(long) == sizeof(long long)` and fix the path to check for `get_dtype()->typeobj`.  
  
I may add a compat hack to NumPy, but even if, that cannot stick around forever and it is clearly a `boost::python` bug.

---

## Comment 8

> Username: seberg  
> Created at: 2021-12-17 19:30:12 UTC  
> Updated at: 2021-12-17 19:30:33 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-996982648  

I am proposing a "fix" in NumPy in https://github.com/numpy/numpy/pull/20616.  If merged, it should prevent the crash, but behave identically to just deleting the whole "`equivalent`" code path.  ~Also~ _But_ expect that fix to vanish at some point...  
  
Quite simply: this must be fixed in `boost::python`, since `boost::python` is using the API incorrectly (the compiler should warn about it, but I suppose there is a faulty `reinterpret_cast` involved).

---

## Comment 9

> Username: jakirkham  
> Created at: 2021-12-17 21:34:59 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-997044669  

@stefanseefeld do you have thoughts here? 🙂

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2021-12-17 21:41:58 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-997047759  

I'm looking into it. Meanwhile, @TallJimbo , do you remember the origin (and rationale) behind this code ?

---

## Comment 11

> Username: seberg  
> Created at: 2021-12-17 21:49:43 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-997051366  

Thanks.  The code makes some sense: NumPy scalars can wrap e.g. C `long` or `long long` and depending on the platform, some of those will always end up being identical.  (Also double and long double, but frankly, whatever.)  
  
Trying to use `EquivTypes` (on the descriptors rather than types) might work but is probably just a pain to get truly right.  My suggestion would be to fix that windows special case (using only the scalar types, no dtypes involved) and add one for `long == long long`, and hope that is good enough.  
There might be some stranger things that the current code allows (e.g. subclasses or some probably non-existing user dtypes), but overall that seems like the safe bet (and probably the only safe bet, just because something is equivalent on the dtype level doesn't mean you can read its C data like you currently do).

---

## Comment 12

> Username: TallJimbo  
> Created at: 2021-12-17 23:01:14 UTC  
> Updated at: 2021-12-17 23:01:34 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-997079639  

@seberg nailed the original motivation.  It is entirely possible that my use of `EquivTypes` was always faulty, and I thought I was using it correctly just because it made various things work when `sizeof(int) == sizeof(long)` or `sizeof(long) == sizeof(long long)`.  Note that if 32-bit architectures are still supported here, you probably do want to special-case the former as well.

---

## Comment 13

> Username: bkpoon  
> Created at: 2024-06-06 17:21:15 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-2153040063  

Sorry to bring this up again, but this error is appearing again in the numpy 2.0 release candidates because the fix was removed in this commit  
  
https://github.com/numpy/numpy/commit/a914726f3b68f4b0d5526a051c3ba6ab1b19f55b#diff-71aadf0228c9cd61ea260748af1dcd4a726eea8476b8cbd667367466c1e39077  
  
What can be a way forward? Thanks!  
  
@stefanseefeld @TallJimbo @seberg @rgommers

---

## Comment 14

> Username: seberg  
> Created at: 2024-06-06 19:35:13 UTC  
> Updated at: 2024-06-07 11:51:40 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-2153269192  

Would it be super hard to just fix it in boost::python?  I don't really like the idea of dragging around a hack in NumPy for another 3 years and 2.0 needs everyone to bump boost versions anyway?!  
  
EDIT: To be clear: this should be easy to fix so the main question may be how hard it is to make a bug-fix release that includes it soon enough to not block projects on.

---

## Comment 15

> Username: bkpoon  
> Created at: 2024-06-22 19:11:57 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-2184159534  

I cleared some items off my plate so I can try to solve this over the next few weeks. On conda-forge, it should be pretty easy to patch the currently pinned version of the Boost conda package since compatibility with Numpy 2.0 was previously added (https://github.com/conda-forge/boost-feedstock/pull/198). @jakirkham would know more.  
  
I'm guessing the approach would be to change the `equivalent` function to check for non-Numpy types and returning False immediately before calling `PyArray_EquivTypes`?

---

## Comment 16

> Username: seberg  
> Created at: 2024-06-25 07:38:04 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-2188195710  

@bkpoon, the no-op change is to just use `==` instead of the function.  That might not be what you want precisely, but it is exactly what you had anyway, I think.

---

## Comment 17

> Username: bkpoon  
> Created at: 2024-07-16 16:49:29 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-2231387816  

Hi all,  
  
Sorry for the delay, I was traveling for a conference.  
  
I made a pull request to reduce the check to directly comparing the pointers (thanks @seberg). It does seem to work for me. I built a patched version of the Boost conda packages and tested it on my use cases (we were getting segmentation faults for C++ arrays we were exposing to Python via Boost). It does work for us.  
  
Prior to the fix, our Numpy 2 migration on conda-forge fails.  
https://github.com/conda-forge/cctbx-base-feedstock/pull/93  
  
I adjusted that PR to pull my patched Boost libraries from my channel and the segmentation faults go away.  
https://github.com/conda-forge/cctbx-base-feedstock/pull/94  
https://github.com/bkpoon/boost-feedstock/tree/np2_fix  
  
The patched Boost packages are available here.  
https://anaconda.org/cctbx-dev  
  
@stefanseefeld can you review the PR? I completely removed the old code path, but we can use the `NPY_ABI_VERSION` check to keep the old code path for Numpy versions less than 2. Just let me know.  
  
Thanks!

---

## Comment 18

> Username: stefanseefeld  
> Created at: 2024-07-16 18:18:23 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-2231530506  

Thanks for all the work ! (And despite the actual patch to look quite simple, I have been removed from low-level Python & Numpy code in recent years that I find it increasingly difficult to review such changes.  
  
Let's see when the next window of opportunity is to merge the `develop` branch to `master`...

---

## Comment 19

> Username: bkpoon  
> Created at: 2024-07-16 19:31:12 UTC  
> Url: https://github.com/boostorg/python/issues/376#issuecomment-2231686994  

I'll get the pull requests for `boost-feedstock` so that the changes can be in the pinned and latest Boost packages. @jakirkham I should have them ready some time today or tomorrow.  
  
@stefanseefeld Feel free to ping me if there are issues. Thanks!
