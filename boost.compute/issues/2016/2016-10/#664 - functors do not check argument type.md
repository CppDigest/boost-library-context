# #664 - functors do not check argument type [Open]

> Username: Ulfgard  
> Created at: 2016-10-03 17:56:16 UTC  
> Updated at: 2017-04-24 16:40:12 UTC  
> Url: https://github.com/boostorg/compute/issues/664  

The following compiles but fails at run-time:  
  
```  
boost::compute::vector<float> a(100,1.0);  
double t = 3.2;  
auto p = boost::compute::bind(boost::compute::pow<float>(),boost::compute::placeholders::_1, t);  
boost::compute::transform(a.begin(),a.end(),a.begin(),p);  
```  
  
The issue is that pow can only take arguments of one floating point type*. and while the functor is declared as T(T,T) there is no place where it is actually checked that arguments match leading to an awkward runtime error.  
  
*https://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/pow.html  
  
> This built-in math function takes scalar or vector arguments. The generic type name gentype is used to indicate that the function can take float, float2, float4, float8, or float16 as the type for the arguments. For any specific use of these function, the actual type has to be the same for all arguments and the return type.   
##
