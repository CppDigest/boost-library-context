# #834 - user_rounding_error template signature [Closed]

> Username: mjhilt  
> Created at: 2022-10-05 20:27:24 UTC  
> Updated at: 2022-10-09 08:14:28 UTC  
> Closed at: 2022-10-09 08:14:28 UTC  
> Url: https://github.com/boostorg/math/issues/834  

In policies/error_handling.hpp the `user_rounding_error` template signature is currently defined as  
```  
template <class T, class TargetType>  
T user_rounding_error(const char* function, const char* message, const T& val, const TargetType& t);  
```  
  
And then it is used in  
```  
template <class T, class TargetType>  
inline TargetType raise_rounding_error(  
           const char* function,  
           const char* message,  
           const T& val,  
           const TargetType& t,  
           const  ::boost::math::policies::rounding_error< ::boost::math::policies::user_error>&)  
{  
   return user_rounding_error(function, message, val, t);  // <---- note the implicit cast from T to TargetType here  
}  
```  
  
The implicit cast hints that this might not be the intended use of the user defined function (perhaps result of a copy/paste error)? I would guess the correct signature would be to return a `TargetType` instead.  
  
If I'm incorrect and the intention really is for a cast to take place there, can you add a `static_cast`-like wrapper so compliers don't complain about possible loss of precision (which is how I found this in the first place)?
