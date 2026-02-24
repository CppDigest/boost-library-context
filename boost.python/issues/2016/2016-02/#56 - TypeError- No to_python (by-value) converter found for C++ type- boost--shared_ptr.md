# #56 - TypeError: No to_python (by-value) converter found for C++ type: boost::shared_ptr<...> [Closed]

> Username: wojdyr  
> Created at: 2016-02-11 13:32:15 UTC  
> Updated at: 2016-03-06 17:58:40 UTC  
> Closed at: 2016-03-06 17:58:40 UTC  
> Url: https://github.com/boostorg/python/issues/56  

This is already being discussed in the hijacked issue #29 but since it's a different thing - regression between 1.59 and 1.60 - let's open a new issue for it. To summarize what I just googled:  
  
The problem is that converters to_python are not created automatically for boost::shared_ptr now.  
It was also raised [a month ago on C++-sig](https://mail.python.org/pipermail/cplusplus-sig/2016-January/017362.html) -- although workaround is known it'd be better to not have to add it everywhere.  
  
@bennybp [commented](https://github.com/boostorg/python/issues/29#issuecomment-179570851):  
  
> I managed to bisect with a simple test program and script. It appears to come from an update/rewrite of type_traits around commit boostorg/type_traits@f0da159e1f52e87ad71e4747de6b042c54578e0e (in the type_traits submodule, although that commit won't compile for me).  
>   
> Unfortunately, it looks like a big rewrite in one commit, so I don't know the exact cause, but I hope this helps  
  
This issue was found in many projects that use boost.python: cctbx, mapnik/python-mapnik#79, BVLC/caffe#3494

---

## Comment 1

> Username: tomhughes  
> Created at: 2016-02-11 13:43:34 UTC  
> Url: https://github.com/boostorg/python/issues/56#issuecomment-182870953  

Just to be clear, the ones in https://github.com/mapnik/python-mapnik were `std::shared_ptr` not `boost::shared_ptr` but they were working without explicit registration with previous versions of boost.
