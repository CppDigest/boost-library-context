# #457 - Bug with OpenMP and python 3.12 [Closed]

> Username: efp  
> Created at: 2024-11-14 16:45:14 UTC  
> Updated at: 2024-11-14 22:18:34 UTC  
> Closed at: 2024-11-14 22:18:34 UTC  
> Url: https://github.com/boostorg/python/issues/457  

Greetings. There is a problem using boost python with python 3.12 and OpenMP, surely related to this: [https://stackoverflow.com/questions/78200321/python3-12-c-api-segfaults-with-openmp](https://stackoverflow.com/questions/78200321/python3-12-c-api-segfaults-with-openmp)  
  
The following program works fine with python 3.6, but segfaults with 3.12:  
  
```  
#include <boost/python.hpp>  
#include <Python.h>  
#include <omp.h>  
  
#include <iostream>  
  
int main(int argc, char* argv[])  
{  
    Py_Initialize() ;  
  
    namespace bp = boost::python ;  
  
    const auto max_threads = omp_get_max_threads() ;  
    std::cout << "threads: " << max_threads << std::endl ;  
  
    bp::object x ;  
  
    #pragma omp parallel for  
    for (int i=0; i<100; ++i)  
    {   
        #pragma omp critical  
        x = bp::eval("1 + 1") ;  
    }  
  
    std::cout << "1 + 1 is " << bp::extract<int>(x) << std::endl ;  
}  
```  
  
It will also work fine in 3.12 if you comment out the parallel directive.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2024-11-14 16:52:37 UTC  
> Url: https://github.com/boostorg/python/issues/457#issuecomment-2476938355  

If this is a known issue with Python's C API, why are you reporting it for Boost.Python ?

---

## Comment 2

> Username: efp  
> Created at: 2024-11-14 17:02:06 UTC  
> Url: https://github.com/boostorg/python/issues/457#issuecomment-2476960312  

As I understand it, it's not an issue with the C API, it's an issue with not correctly using the GIL, which did not cause problems until 3.12.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2024-11-14 18:27:22 UTC  
> Url: https://github.com/boostorg/python/issues/457#issuecomment-2477130640  

Oh, indeed, the GIL ! :-)  
Yes, so your code snippet above is not thread-safe. You should acquire the GIL within the for-loop prior to running `eval`, to serialize access to the Python runtime.  
  
E.g.  
  
`struct gil_lock  
{  
  gil_lock() : state(PyGILState_Ensure()) {}  
  ~gil_lock() { PyGILState_Release(state);}  
  PyGILState_STATE state;  
};  
  
struct gil_unlock  
{  
  gil_unlock() : save(PyEval_SaveThread()) {}  
  ~gil_unlock() { PyEval_RestoreThread(save);}  
  PyThreadState *save;  
};  
  
...  
gil_unlock unlock;  
#pragma omp parallel for  
for (int i=0; i<100; ++i)  
{  
    gil_lock lock;  
    #pragma omp critical  
    x = bp::eval("1 + 1") ;  
}  
  
`

---

## Comment 4

> Username: efp  
> Created at: 2024-11-14 20:43:06 UTC  
> Url: https://github.com/boostorg/python/issues/457#issuecomment-2477373036  

Thanks for the reply... I would have thought boost python (bp) would acquire & release the GIL around all of the C API function calls itself, otherwise one would have to do it around virtually all bp calls, and we shouldn't have to mix bp & the C API.   
  
Prior to python 3.12 it seems the omp critical section was enough to make it thread safe. Now something about the per-thread python interpreter state is causing problems. In theory the PyGILState_Ensure() and PyGILState_Release() statements should take care of that, even though it seems redundant with the critical block. I suppose using the GIL lock we should be able to do away with the critical block.  
  
But, your solution still segfaults; I've done more experiments and they also all segfault, so maybe there is a lower level problem. Still experimenting...

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2024-11-14 21:40:50 UTC  
> Url: https://github.com/boostorg/python/issues/457#issuecomment-2477464315  

The whole point of the GIL is to reduce the need to acquire & release on a fine-grained level as that would kill performance. That is, locking is something done only as needed. Still, I get your point, Boost.Python could at least provide some more convenient API, including call-policies to indicate whether or not a call should perform any such lock acquisition / release.  
I wonder how this whole thing is going to evolve as with Python 3.13 the GIL is optional. We shall see...

---

## Comment 6

> Username: efp  
> Created at: 2024-11-14 22:18:34 UTC  
> Url: https://github.com/boostorg/python/issues/457#issuecomment-2477518790  

So, to close this out: it appears what prevented your solution from working was my final   
  
`std::cout << "1 + 1 is " << bp::extract<int>(x) << std::endl ;`  
  
We had to restore the original thread state before doing that, as 'x' was declared outside the parallel section. So I just needed to enclose your gil_unlock in a scope.   
  
Thanks, and cheers.
