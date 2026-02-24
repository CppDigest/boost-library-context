# #219 - About adding a static check(python::object) method to boost::python::numpy [Open]

> Username: 7starsea  
> Created at: 2018-07-16 07:53:31 UTC  
> Updated at: 2018-07-16 07:53:31 UTC  
> Url: https://github.com/boostorg/python/issues/219  

I am currently using boost::python::numpy module which make binding from c++ to python very easy.  
Sometimes, I need to check whether the python object is a PyArray type. However, I could not find a similar method like PyArray_Check (raw numpy_api) in boost::python::numpy, so I am wondering could we add a static check method to boost::python::numpy.  
  
Thanks.  
Aimin
