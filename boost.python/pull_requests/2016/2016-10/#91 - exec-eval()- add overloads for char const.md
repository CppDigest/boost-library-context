# #91 exec/eval(): add overloads for char const* [Merged]

> Username: jzwinck  
> Created at: 2016-10-21 08:02:46 UTC  
> Updated at: 2017-05-04 23:31:05 UTC  
> Merged at: 2017-05-04 23:29:57 UTC  
> Closed at: 2017-05-04 23:29:57 UTC  
> Url: https://github.com/boostorg/python/pull/91  

Many times the caller may have a string created in C++,  
so there is no need to wrap it in a Python object when  
the only thing done with the object is extract<char*>.

---

## Comment 1

> Username: jzwinck  
> Created_at: 2017-02-12 13:47:14 UTC  
> Url: https://github.com/boostorg/python/pull/91#issuecomment-279219635  

@stefanseefeld Perhaps I could ask you to review this?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2017-02-12 17:07:39 UTC  
> Url: https://github.com/boostorg/python/pull/91#issuecomment-279232310  

What problem are you trying to solve with this patch ?

---

## Comment 3

> Username: jzwinck  
> Created_at: 2017-02-13 10:36:58 UTC  
> Updated_at: 2017-02-13 10:37:57 UTC  
> Url: https://github.com/boostorg/python/pull/91#issuecomment-279350259  

@stefanseefeld This patch solves the problem that if the string you want to exec or eval is a C string literal or char array (both of which are common), you previously needed to construct a temporary boost::python::str from your C string just to pass it to Python's C API.  Constructing such temporary requires a call to `PyObject_MALLOC()` (via `PyString_FromString()` in Python 2).  
  
In other words, this patch avoids unnecessary memory allocation, copying, and scanning of strings which may be very long.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2017-02-13 12:43:53 UTC  
> Url: https://github.com/boostorg/python/pull/91#issuecomment-279379745  

Just for avoidance of doubt, the following code works fine:  
```  
boost::python::eval("some python code");  
```  
i.e., the wrapping of a `char const *` in a python string is implicit.  
I understand your concern with overhead due to unnecessary copying of (long) strings, but am not sure to what degree this is a problem in practice. Are you dealing with a case where this incurs measurable overhead, or is this a theoretical concern only ?

---

## Comment 5

> Username: jzwinck  
> Created_at: 2017-02-13 13:05:02 UTC  
> Url: https://github.com/boostorg/python/pull/91#issuecomment-279387173  

@stefanseefeld Yes I am aware of the implicit conversion.  But as I'm sure you know, C++ and Boost users are quite accustomed to only paying for what they need, and not incurring unnecessary runtime overhead.  This patch is very straightforward, and does not make the library harder to use or understand, so I do not feel the need to justify it with performance benchmarks.  Avoiding unnecessary allocation, copying, and scanning of strings whose lengths are not knowable to us should be reason enough to consider this simple patch.

---

## Comment 6

> Username: jzwinck  
> Created_at: 2017-05-04 13:31:57 UTC  
> Url: https://github.com/boostorg/python/pull/91#issuecomment-299185745  

@stefanseefeld Would you please consider merging this?  If not I intend to close it.

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2017-05-04 23:31:05 UTC  
> Url: https://github.com/boostorg/python/pull/91#issuecomment-299336928  

Yes, done. Sorry it too me so long !   
(In fact, I think I'm going to deprecate the original functions as there is little use in passing python `str` objects when starting from the C++ side.)

---
