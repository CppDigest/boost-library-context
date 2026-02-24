# #736 - Anyway to define and use other utilities function within the lambda functions? [Open]

> Username: Naimin  
> Created at: 2017-08-02 04:13:34 UTC  
> Updated at: 2017-08-02 04:14:33 UTC  
> Url: https://github.com/boostorg/compute/issues/736  

Just wondering how do I pass/declare a helper function to the kernel function so they can be used within BOOST_COMPUTE_FUNCTION or BOOST_COMPUTE_CLOSURE.  
  
I saw the BOOST_COMPUTE_DECLARE_BUILTIN_FUNCTION macro but not quite sure how to use it or even if it is the correct way to declare a new function that is accessible from within a lambda.  
  
  
Something in the range of this example, basically trying to inline this foo() into bar().  
```  
inline float foo()  
{  
  return -1.0f;  
}  
  
BOOST_COMPUTE_FUNCTION(float, bar, (float in),   
{  
   return in + foo();  
})  
```  
  
Any advise is appreciated, thanks.
