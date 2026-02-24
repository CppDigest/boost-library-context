# #41 - Cannot run histogram_test on MacOS: libboost_serialization.dylib not loaded [Closed]

> Username: Axel-Naumann  
> Created at: 2017-12-13 19:14:27 UTC  
> Updated at: 2018-03-06 21:45:42 UTC  
> Closed at: 2018-03-06 21:45:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/41  

I get  
```  
○ → ./histogram_test  
dyld: Library not loaded: libboost_serialization.dylib  
  Referenced from: /Users/axel/build/tmp/HDembinski-histogram/cmake/./histogram_test  
  Reason: image not found  
Abort trap: 6  
```  
  
Thanks to SIP, this doesn't help either:  
```  
○ → LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/Users/axel/build/boost/inst/lib ./histogram_test  
dyld: Library not loaded: libboost_serialization.dylib  
  Referenced from: /Users/axel/build/tmp/HDembinski-histogram/cmake/./histogram_test  
  Reason: image not found  
Abort trap: 6  
○ → ls -l /Users/axel/build/boost/inst/lib/libboost_serialization.dylib  
-rwxr-xr-x  1 axel  staff  578644 Dec 13 17:43 /Users/axel/build/boost/inst/lib/libboost_serialization.dylib  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2017-12-14 12:50:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/41#issuecomment-351702308  

I deactivated SIP a while ago. For developers it is a real hassle, lldb does not work with it. This seems relevant:  
  
https://groups.google.com/forum/#!topic/caffe-users/waugt62RQMU  
  
It might work even with SIP enabled, if you install boost with `brew`. My boost paths are neither in LD_LIBRARY_PATH, nor in DYLD_LIBRARY_PATH, and the libraries are found.  
  
`brew install boost`  
`brew install boost-python`  
  
A quick solution may be to soft-link the libboost_serialization.dylib to your build directory.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-03-06 21:45:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/41#issuecomment-370939829  

This issue has not seen any activity, so I assume the problem is fixed by my reply. Closing...
