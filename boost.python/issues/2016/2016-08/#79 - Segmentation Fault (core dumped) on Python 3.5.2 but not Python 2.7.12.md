# #79 - Segmentation Fault (core dumped) on Python 3.5.2 but not Python 2.7.12 [Closed]

> Username: mpconte  
> Created at: 2016-08-04 17:19:56 UTC  
> Updated at: 2018-02-16 14:59:49 UTC  
> Closed at: 2017-07-20 18:05:58 UTC  
> Url: https://github.com/boostorg/python/issues/79  

So I'm trying to create a boost python module that simply creates and returns a numpy array,   
but the function crashes (sometimes) and it doesn't ever seem to crash on Python 2.  
  
Here's the source code I made:  
  
```  
#include <boost/python.hpp>  
#include <numpy/ndarrayobject.h>  
  
using namespace boost::python;  
  
object create_numpy_array() {  
    npy_intp dims = 1;  
    long* data = new long[1];  
    data[0] = 1;  
    PyObject* obj = PyArray_SimpleNewFromData(1, &dims, PyArray_LONGLTR, data);  
    boost::python::handle<> handle(obj);  
    boost::python::numeric::array arr(handle);  
    return arr.copy();  
}  
  
BOOST_PYTHON_MODULE(create) {  
    import_array();  
    numeric::array::set_module_and_type("numpy", "ndarray");  
    def("numpy_array", &create_numpy_array);  
}  
  
```  
  
using a simple python script to test:  
  
```  
import create  
print(create.numpy_array())  
  
```  
  
The stack trace indicates that the crash occurs on a boost::python::handle destructor trying to decrease the ref count of a PyObject with a ref count of 0.  
  
```  
boost_python3-vc140-mt-gd-1_60.dll!boost::python::xdecref<_object>(_object * p)  Line 36 + 0x5b bytes   C++  
boost_python3-vc140-mt-gd-1_60.dll!boost::python::handle<_object>::~handle<_object>()  Line 184 + 0xd bytes C++  
boost_python3-vc140-mt-gd-1_60.dll!boost::python::numeric::`anonymous namespace'::`dynamic atexit destructor for 'array_function''()  + 0x10 bytes  C++  
  
```  
  
I've tried this on both Windows 7 and Ubuntu 16.04 both 64-bit.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-08-04 19:04:30 UTC  
> Updated at: 2016-08-04 19:05:15 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237651629  

Could you show the exact command you used to compile your module ?  
  
I wasn't able to compile your code, as with Python 3 it is wrong: the `import_array()` function on Python 3 returns `NULL`, while the `BOOST_PYTHON_MODULE(create)` macro call generates a function returning `void`. To fix this I wrapped the `import_array()` call into its own function:  
  
```  
#if PY_VERSION_HEX >= 0x03000000  
void *  
#else  
void  
#endif  
initialize()  
{  
  import_array();  
}  
  
BOOST_PYTHON_MODULE(create) {  
  initialize();  
  numeric::array::set_module_and_type("numpy", "ndarray");  
    def("numpy_array", &create_numpy_array);  
}  
```  
  
And with that change, the module didn't produce a segmentation fault when I called the `create.numpy_array()` function. (I'd suspect that due to the above error, you managed to compile the code in a way that NumPy was never actually initialized properly, which produces the symptoms you observe.)  
  
Let me know whether you still observe an issue after applying the change I suggest.  
(I should note that I only tested this on GNU/Linux (Fedora 24) using g++ 6.1.)

---

## Comment 2

> Username: mpconte  
> Created at: 2016-08-04 19:20:38 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237655933  

Yes, I indeed created a wrapper as you suggested, but I still get the same  
error.  
  
I've actually found an issue addressing this:  
  
https://github.com/boostorg/python/issues/75  
  
On Thu, Aug 4, 2016 at 3:04 PM, Stefan Seefeld notifications@github.com  
wrote:  
  
> Could you show the exact command you used to compile your module ?  
>   
> I wasn't able to compile your code, as with Python 3 it is wrong: the  
> import_array() function on Python 3 returns NULL, while the  
> BOOST_PYTHON_MODULE(create) macro call generates a function returning void.  
> To fix this I wrapped the import_array() call into its own function:  
>   
> #if PY_VERSION_HEX >= 0x03000000  
> void *  
> #else  
> void  
> #endif  
> initialize()  
> {  
>   import_array();  
> }  
>   
> BOOST_PYTHON_MODULE(create) {  
>   initialize();  
>   numeric::array::set_module_and_type("numpy", "ndarray");  
>     def("numpy_array", &create_numpy_array);  
> }  
>   
> And with that change, the module didn't produce a segmentation fault when  
> I called the create.numpy_array() function. (I'd suspect that due to the  
> above error, you managed to compile the code in a way that NumPy was never  
> actually initialized properly, which produces the symptoms you observe.)  
>   
> Let me know whether you still observe an issue after applying the change I  
> suggest.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/python/issues/79#issuecomment-237651629, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AHlt-3cWJ7qp90HApDCxpuIcKh0M30wzks5qcje_gaJpZM4Jc7R3  
> .

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2016-08-04 19:24:18 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237656827  

On 04.08.2016 15:20, Matthew wrote:  
  
> Yes, I indeed created a wrapper as you suggested, but I still get the same  
> error.  
  
That's unfortunate, as that implies I can't reproduce the issue. I'm  
still interested in knowing exactly how you built your extension module  
(both on Linux as well as Windows), and what exact versions of libraries  
(Python, Boost, NumPy) and tools (compilers) you are using.  
  
##   
  
```  
  ...ich hab' noch einen Koffer in Berlin...  
```

---

## Comment 4

> Username: mpconte  
> Created at: 2016-08-04 19:38:08 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237660400  

Both versions used:  
  
python 3.5.2  
boost 1.60  
numpy 1.11.1  
  
Windows used the Visual Studio 2014 compiler.  
  
Ubuntu used the standard gnu g++ compiler that came with it.  
  
Also, try to run the program several times, eventually it will crash (at  
least it did for me).  
  
The issue I provided the link for explains that writing a simple wrapper  
for the boost numeric array will resolve the issue:  
  
He included a link to resolve it here:  
  
https://github.com/mantidproject/mantid/commit/7371d129eca58daa6ca1e8852c8de736f2cefc66  
  
On Thu, Aug 4, 2016 at 3:24 PM, Stefan Seefeld notifications@github.com  
wrote:  
  
> On 04.08.2016 15:20, Matthew wrote:  
>   
> > Yes, I indeed created a wrapper as you suggested, but I still get the  
> > same  
> > error.  
>   
> That's unfortunate, as that implies I can't reproduce the issue. I'm  
> still interested in knowing exactly how you built your extension module  
> (both on Linux as well as Windows), and what exact versions of libraries  
> (Python, Boost, NumPy) and tools (compilers) you are using.  
>   
> ##   
>   
> ...ich hab' noch einen Koffer in Berlin...  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/python/issues/79#issuecomment-237656827, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AHlt-0teCmledgstfZP4F7XIRmJJQ3Jlks5qcjxjgaJpZM4Jc7R3  
> .

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2016-08-04 19:41:25 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237661254  

Thanks. Please note that I have plans to add proper support for NumPy to Boost.Python, which will obsolete the current "numeric" API.

---

## Comment 6

> Username: mpconte  
> Created at: 2016-08-05 13:12:09 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237846432  

That's awesome! Are you referring to the Boost.Numpy extension?  
  
https://github.com/ndarray/Boost.NumPy  
  
and may I ask what date or version of Boost this may be released?  
  
On Thu, Aug 4, 2016 at 3:41 PM, Stefan Seefeld notifications@github.com  
wrote:  
  
> Thanks. Please note that I have plans to add proper support for NumPy to  
> Boost.Python, which will obsolete the current "numeric" API.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/python/issues/79#issuecomment-237661254, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AHlt-yJ9y7v4i0f-v8pTF8ztKohP4eYMks5qckBlgaJpZM4Jc7R3  
> .

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2016-08-05 13:17:31 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237847609  

Yes, that's the code base I'm talking about. I don't have any specific plans / time frame yet, as there are a couple of other high priority tasks I intend to address (such as C++ 11 support).  
Needless to say, any help would be more than welcome !

---

## Comment 8

> Username: mpconte  
> Created at: 2016-08-05 13:33:04 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237851152  

How could I help?  
  
On Aug 5, 2016 09:17, "Stefan Seefeld" notifications@github.com wrote:  
  
> Yes, that's the code base I'm talking about. I don't have any specific  
> plans / time frame yet, as there are a couple of other high priority tasks  
> I intend to address (such as C++ 11 support).  
> Needless to say, any help would be more than welcome !  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/python/issues/79#issuecomment-237847609, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AHlt-zHur34IqjJbYHEU-LYQsKmP9wSPks5qczfrgaJpZM4Jc7R3  
> .

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2016-08-05 14:19:30 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-237862853  

Good question. Right now my highest priority is to wrap up the work on the new SCons-based build system (which allows me to build Boost.Python stand-alone, against a pre-installed Boost). This is almost complete, but it would be good to have some additional eyes / hands on it.  
With that fixed, it should be relatively easy to incrementally pull in chunks from https://github.com/ndarray/Boost.NumPy.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2016-10-08 20:20:43 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-252446248  

Just as a FYI: I have just merged a new NumPy C++ API extension into the development branch, and plan to release that with Boost 1.63. (It still lacks integration with Boost's own build system, as I'm mostly just working with Boost.Python's SCons-based build logic nowadays.)  
I encourage you to give that a try, as I also plan to deprecate the original "numeric" API.

---

## Comment 11

> Username: stefanseefeld  
> Created at: 2017-07-20 18:05:58 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-316784627  

Note that the `numeric` module (namespace, header, etc.) have just been removed as they were obsoleted by the recent addition of proper NumPy support. Please update your code to use that instead.

---

## Comment 12

> Username: ofloveandhate  
> Created at: 2018-02-16 14:29:38 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-366250288  

when you say  
  
> Please update your code to use that instead  
  
can you please give a link?  thanks for all your work on boost python!

---

## Comment 13

> Username: stefanseefeld  
> Created at: 2018-02-16 14:51:26 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-366256109  

Please follow the instructions from the current Boost NumPy documentation (e.g., http://boostorg.github.io/python/doc/html/numpy/index.html). This has been in the last couple Boost releases, so you should be able to use the Boost.NumPy API with one of these.

---

## Comment 14

> Username: ofloveandhate  
> Created at: 2018-02-16 14:59:49 UTC  
> Url: https://github.com/boostorg/python/issues/79#issuecomment-366258542  

thankyou!!!
