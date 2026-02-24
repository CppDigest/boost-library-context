# #419 Fix for issue - https://github.com/boostorg/asio/issues/414 [Closed]

> Username: SilverPlate3  
> Created at: 2023-06-28 18:07:31 UTC  
> Updated at: 2023-06-29 07:03:19 UTC  
> Closed at: 2023-06-29 07:03:19 UTC  
> Url: https://github.com/boostorg/asio/pull/419  

Issue 1: Double std::move() in operator() function:  
  
In the original code, std::move() was used twice when constructing result_type, which is unnecessary.  
The modification replaces the second std::move() with std::forward(ts), ensuring proper forwarding of the arguments.  
This change avoids double-moving the arguments and allows them to be forwarded correctly to the constructor of result_type.  
Issue 2: Unnecessary std::move() in apply_impl function:  
  
In the original code, std::move() was used unnecessarily within the std::get() call.  
The modification replaces std::move(result_type) with std::forward(std::get(result_type)).  
This change correctly forwards the elements of result_type to the function f, ensuring the optimal value category is preserved.  
These modifications address the specific issues related to std::move() in the promise.hpp  
issue#414

---
