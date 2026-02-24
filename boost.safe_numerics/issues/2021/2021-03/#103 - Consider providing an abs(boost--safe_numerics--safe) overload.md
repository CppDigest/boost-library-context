# #103 - Consider providing an abs(boost::safe_numerics::safe) overload [Open]

> Username: Eelis  
> Created at: 2021-03-29 16:47:58 UTC  
> Updated at: 2021-03-29 16:53:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/103  

The lack of such an overload has two drawbacks:  
  
1. If one currently has code like:  
```  
int main()  
{   
    using I = boost::safe_numerics::safe<int32_t>;  
    I x = INT32_MAX;  
    I y = abs(x)+abs(x); // silently overflows  
    std::cout << y; // prints -2  
}  
```  
then the overflow is not detected. If SafeNumerics provided its own `abs` overload for `safe` returning a `safe`, then the overflow could be detected and signaled.  
  
2. If one currently has perfectly valid code like:  
```  
using I = int32_t;  
using std::abs;  
I x = 3, y = abs(x);  
```   
   then changing the first line to `using I = boost::safe_numerics::safe<int32_t>;` makes the code ill-formed, due to the ambiguity resulting from the fact that `boost::safe_numerics::safe` is willing to convert to the parameter type of multiple `std::abs` overloads.
