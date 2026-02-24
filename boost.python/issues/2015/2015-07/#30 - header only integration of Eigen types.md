# #30 - header only integration of Eigen types [Open]

> Username: nevion  
> Created at: 2015-07-27 05:26:03 UTC  
> Updated at: 2015-10-21 15:00:06 UTC  
> Url: https://github.com/boostorg/python/issues/30  

If you google around a bit, you'll see a number of users trying to integrate Eigen types with Boost.Python.  It's a bit hard and the solutions are lack-luster and cannot handle all issues.  Let's strive for something built-in and header based so we don't complicate other uses of Boost.Python.  
  
Here's an incomplete list:  
- http://fhtagn.net/prog/2015/04/16/quaternion_boost_python.html  
- https://github.com/julienr/boost_numpy_eigen  
- http://eigen.tuxfamily.org/index.php?title=PythonInteropBoost  
- https://github.com/eudoxos/minieigen  
- https://github.com/ndarray/ndarray  (note: depends on Boost.Numpy)

---

## Comment 1

> Username: eudoxos  
> Created at: 2015-09-27 09:53:16 UTC  
> Url: https://github.com/boostorg/python/issues/30#issuecomment-143533947  

Hi, I'm the author of minieigen.  
  
A big issue is that in Eigen, all intermediary results are expression templates, thus each of them is a new type. That's why many operators need to wrapped so that they return the expression template evaluated. If you have some initial implementation of how to go header-only, that would be great.  
  
Also note that minieigen is almost entirely header-only (and templated), it just has a few small .cpp files which register the wrappers in python.

---

## Comment 2

> Username: nevion  
> Created at: 2015-09-27 20:19:08 UTC  
> Url: https://github.com/boostorg/python/issues/30#issuecomment-143591899  

Hi @eudoxos , I am aware of your work on minieigen and I remember when you announced it on the eigen ML a while back :-)  
  
After having to do alot of bindings between C++ and eigen in function signatures, I found the path of least resistance, though still required a decent amount of registration calls, was to implicitly map eigen types to/from numpy arrays.  This means that operations are performed based on numpy code and it's like a shared memory communication.  The bad thing is that now there will be subtle floating point differences between eigen implemented functions and numpy's.  On the other hand, it drastically reduced the complexity of Eigen's soup of expression templates and other compile time/type issues.  Perhaps the functions we'd want to export for using eigen to implement some operations on numpy ndimages would then provide the way to use Eigen to provide the implementation when needed.  
  
I think this actually might be the best path forward, especially if Boost.Numpy gets integrated - what do you think?

---

## Comment 3

> Username: eudoxos  
> Created at: 2015-10-01 15:48:38 UTC  
> Url: https://github.com/boostorg/python/issues/30#issuecomment-144768017  

Hi @nevion, that sounds like a very good idea, one will have numpy or eigen use the same chunk of memory as input for its ops. I am not very familiar with numpy on the memory level, can't help much unfortunately. Is Eigen's alignment a possible issue, or that is handled fine by numpy?  
  
The only drawback which I see is that people will have hard(er) time transitioning from numpy to eigen (from python in c++) -- the advantage of minieigen is that you can prototype the code in python and almost cut&paste into c++ and it will do the same thing.   
  
By all means, go forward.

---

## Comment 4

> Username: eudoxos  
> Created at: 2015-10-21 15:00:06 UTC  
> Url: https://github.com/boostorg/python/issues/30#issuecomment-149924177  

Recommended: http://pybind11.readthedocs.org/en/latest/, a c++11-only lighter, header-only evolution of good old boost::python. Especiall the chapter on buffer protocol and numpy arrays.
