# #357 - docutils.html seems to ignore the build dir? [Closed]

> Username: pdimov  
> Created at: 2018-10-16 00:51:38 UTC  
> Updated at: 2018-10-16 02:31:21 UTC  
> Closed at: 2018-10-16 02:31:21 UTC  
> Url: https://github.com/boostorg/build/issues/357  

When I try to build the `boostrelease` target in `libs/parameter_python/doc`, I get  
  
```  
...found 20 targets...  
...updating 2 targets...  
docutils.html /home/pdimov/boost-git/develop/libs/parameter_python/doc/index.html  
common.copy html/index.html  
```  
  
that is, `index.html` is built in the same directory as the source `index.rst`. Why is that? My untrained eye sees nothing wrong with `docutils.jam`.

---

## Comment 1

> Username: pdimov  
> Created at: 2018-10-16 02:31:21 UTC  
> Url: https://github.com/boostorg/build/issues/357#issuecomment-430079378  

Never mind. :-)
