# #479 - templated kernels [Closed]

> Username: tlgimenes  
> Created at: 2015-07-17 08:27:22 UTC  
> Updated at: 2018-08-27 20:25:46 UTC  
> Closed at: 2018-08-27 20:25:46 UTC  
> Url: https://github.com/boostorg/compute/issues/479  

Since not all vendor implementations today can handle templates (nvidia currently only supports OpenCL 1.2), I was wondering if it's possible to add macros and classes to Boost.Compute to parse user defined kernels containing basic templates and then generate a valid OpenCL C code for older OpenCL implementations.  
  
The idea is basically to do something like  
  
```  
 constexpr char type[] = BOOST_STRINGIZE_SOURCE(   
 struct bar   
 {   
      int a;   
      int b;  
  };  
  )        
  
 constexpr char kernel_src[] = BOOST_STRINGIZE_SOURCE(  
 template< typename T >  
 __kernel foo( __global T* bar ) { }   
 )  
  
 constexpr char final_kernel_src = BOOST_RESOLVE_TEMPLATE( kernel_src, type );  
```  
  
The code above is just a demonstration of what I would like to see in Boost.Compute and I'm sure there are better solutions for the problem. I also know that modifying and compiling templated kernels is not that complicated and that each one can do that on their own code, but I would like to see a better and unified way of doing so with the use of Boost.Compute other than have to write my own code for doing this boring work every time  
  
Thank you !  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-07-20 05:14:48 UTC  
> Url: https://github.com/boostorg/compute/issues/479#issuecomment-122765444  

That is an interesting idea, I'll have to think about how it could be implemented. For now, the best way to do this is use `"T"` as a macro and then define it to your desired type (e.g. `-DT=bar` or `-DT=int`) when compiling the program.  
  
Also, you may want to take a look at the `define()` method in the `boost::compute::function` class which allows you to define custom variables which are then compiled when the function is called.

---

## Comment 2

> Username: pavanky  
> Created at: 2015-07-20 08:54:07 UTC  
> Updated at: 2015-07-20 08:54:39 UTC  
> Url: https://github.com/boostorg/compute/issues/479#issuecomment-122815943  

I am not sure any solution to the template problem will be substantially different from passing `-DT=foo`  for the following reasons.  
- You may have multiple template parameters.  
- Template parameters can be more than data types.  
  
Short of writing an OpenCL compiler, the only viable solutions are going to involve macros (as is the case now) or complex regex magic (that is read / test).
