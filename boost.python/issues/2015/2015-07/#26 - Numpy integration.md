# #26 - Numpy integration? [Closed]

> Username: mdboom  
> Created at: 2015-07-16 20:58:38 UTC  
> Updated at: 2016-10-08 20:09:13 UTC  
> Closed at: 2016-10-08 20:09:13 UTC  
> Url: https://github.com/boostorg/python/issues/26  

I'm the author of "numpy-boost", a library to allow access to Numpy arrays through the boost::multi_array interface, as well as integration with the type converters in boost::python.  
  
See http://github.com/mdboom/numpy-boost  
  
Would there be interest in a pull request to add this functionality to boost::python?  
  
Are there contributing guidelines available?

---

## Comment 1

> Username: nevion  
> Created at: 2015-07-27 05:14:18 UTC  
> Updated at: 2015-07-27 05:15:59 UTC  
> Url: https://github.com/boostorg/python/issues/26#issuecomment-125088724  

+1, let's axe the old ndarray support and get this in where it belongs.  Numpy is one of the most common libraries faced with Boost.Python extensions given typical users of Boost.Python are scientific/numerically applications.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-08-05 13:43:20 UTC  
> Url: https://github.com/boostorg/python/issues/26#issuecomment-237853598  

We have been working on NumPy integration in a separate project: https://github.com/ndarray/Boost.NumPy, which I intend to merge into Boost.Python eventually (hopefully soon).

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2016-10-08 20:09:13 UTC  
> Url: https://github.com/boostorg/python/issues/26#issuecomment-252445682  

Since the original issue here was about a separate NumPy binding, I'm going to close this. As I mentioned earlier, integration of an existing NumPy extension is well underway, so this is what we will focus on in the short term.  
Thanks,
