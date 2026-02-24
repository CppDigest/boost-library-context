# #255 - Create diagonal matrix in python::numpy [Open]

> Username: bewantbe  
> Created at: 2019-03-23 15:27:08 UTC  
> Updated at: 2019-03-23 15:27:08 UTC  
> Url: https://github.com/boostorg/python/issues/255  

This is probably a feature request.  
What is the correct way to create diagonal matrix in boost::python::numpy?  
  
Of course, I can just create a usual 2D matrix then assign its diagonal. But is there a better way?  
  
It seems that in numpy (in python), the diagonal matrix is stored in a compact format, e.g. only stores the diagonal data. This can be observed by `a = np.diag(np.random.rand(20000,1))` and watch the memory usage. (much smaller than 20000^2 * 8 / 2^20 MB)  
  
It would be nice to have a method to build diagonal matrix in this "compact" format.
