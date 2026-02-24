# #325 - Accept b = a in quadrature, return zero [Closed]

> Username: NAThompson  
> Created at: 2020-03-19 09:58:27 UTC  
> Updated at: 2020-03-22 12:11:26 UTC  
> Closed at: 2020-03-22 12:11:26 UTC  
> Url: https://github.com/boostorg/math/issues/325  

If b=a in quadrature routines, then:  
  
```  
libc++abi.dylib: terminating with uncaught exception of type boost::wrapexcept<std::domain_error>: Error in function tanh_sinh<double>::integrate: Arguments to integrate are in wrong order; integration over [a,b] must have b > a.  
```  
  
For iterating over quadratures of different domains, it might be easier to allow b = a and just return 0.  
  
Also, if b < a, maybe return -integral from b to a.
