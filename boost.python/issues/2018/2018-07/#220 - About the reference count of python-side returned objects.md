# #220 - About the reference count of python-side returned objects [Open]

> Username: Nvocmoc  
> Created at: 2018-07-17 15:06:56 UTC  
> Updated at: 2018-07-17 15:06:56 UTC  
> Url: https://github.com/boostorg/python/issues/220  

Hi there, if I use boost_python to call a python side function and get a boost::python::object return, it seems that the reference count of wrapped obj.ptr() is at least 1 as they are set to one when created. But I don't think the reference count will be reduced automatically when boost::python::object is destructed (please correct me if I am wrong). If so, how can this memory be freed, should we manually decrease the reference count so that python GC can work? This is especially important if a large object like numpy::ndarray if returned, I am not sure whether C-side or Py-side should take charge of freeing the memory. Thanks in advance!
