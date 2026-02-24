# #808 - use nested custom type with BOOST_COMPUTE_ADAPT_STRUCT macro [Open]

> Username: changephilip  
> Created at: 2018-12-29 15:45:41 UTC  
> Updated at: 2018-12-29 19:35:12 UTC  
> Url: https://github.com/boostorg/compute/issues/808  

Hello!  
I use code like below in my program.  
```c++  
struct MyStructA{  
          int a;  
          int b;  
};  
BOOST_COMPUTE_ADAPT_STRUCT(MyStructA,MyStructA,(a,b))  
struct MyStructB{  
          MystructA c;  
          int d;  
};  
BOOST_COMPUTE_ADAPT_STRUCT(MyStructB,MystructB,(c,d))  
```  
While I compile the program , the compiler  report some errors about redefinition:  
`  
/boost/compute/type_traits/type_name.hpp:119:24: error: redefinition of 'const char* boost::compute::type_name() [with T= MyStructB]'   
`  
  
I wonder whether I could nested a struct in another struct.

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-12-29 19:35:03 UTC  
> Url: https://github.com/boostorg/compute/issues/808#issuecomment-450514984  

I don't thinks that's possible right now. `BOOST_COMPUTE_ADAPT_STRUCT` would have to know dependencies of the struct it's adapting to inject required type definition into kernels.  
  
I think if you look how `BOOST_COMPUTE_ADAPT_STRUCT` is implemented, you can implement similar variadic macro (C++11 would be required and/or GNU extension), where the variadic arguments are dependencies (required types).
