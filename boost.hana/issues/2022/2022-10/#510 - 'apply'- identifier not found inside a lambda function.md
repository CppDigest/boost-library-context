# #510 - 'apply': identifier not found inside a lambda function [Closed]

> Username: atul20031  
> Created at: 2022-10-20 05:44:47 UTC  
> Updated at: 2022-10-21 02:28:31 UTC  
> Closed at: 2022-10-21 02:28:31 UTC  
> Url: https://github.com/boostorg/hana/issues/510  

We are upgrading Boost to 1.78.   
  
On bringing  ```boost::hana::apply/transform``` into the current scope with the help of `using` keyword results in  'identifier not found' error on Windows MSVC 2019 . The error seems to be happening when `apply` or `transform` is accessed inside a lambda function. However, it works fine outside the lambda.    
  
  
Below is the sample code for reference:  
```  
#include <boost/hana/functional/apply.hpp>  
#include <boost/hana/plus.hpp>  
#include <iostream>  
  
int main()  
{  
    using boost::hana::apply;  
  
   // This call works fine  
    static_assert(apply(boost::hana::plus, 1, 2) == 3, "");  
      
    auto myLambda= [](auto d){  
            // This code compiles to 'identifier not found' error  
            static_assert(apply(boost::hana::plus, 1, 2) == 3, "");  
            std::cout<<"Executing lambda \n";  
            };  
  
   myLambda(1);  
   return 0;  
}  
  
```  
  
In the above code, accessing `apply` inside the lambda compiles to 'identifier not found' error, however the first call which is outside the lambda works fine.   
  
Furthermore,  the code compiles successfully if the lambda does not take any argument, for eg:   
  
```  
 auto myLambda= [](){  
            // This call throws 'identifier not found' error  
            static_assert(apply(boost::hana::plus, 1, 2) == 3, "");  
            std::cout<<"Executing lambda \n";  
            };  
  
   myLambda();  
```  
  
This issue is not seen with Boost 1.75 version.  I am not sure why this is happening after upgrading to Boost 1.78. Can someone please have a look?  
  
Compiler error : ```error C3861: 'apply': identifier not found```

---

## Comment 1

> Username: ldionne  
> Created at: 2022-10-21 02:28:31 UTC  
> Url: https://github.com/boostorg/hana/issues/510#issuecomment-1286376657  

I can't reproduce on Godbolt (https://godbolt.org/z/W1qv8j5bz), but this looks like a MSVC issue to me. I don't think we've changed much in that area between 1.75 and 1.78. I suggest you reduce this by inlining the definition of `hana::apply` out of the header and report that to MSVC, without usage of Hana. If they turn out to be conforming and Hana's doing something wrong, ping us and we'll reopen.
