# #66 Fix exception include [Closed]

> Username: pointbazaar  
> Created at: 2024-10-11 09:42:35 UTC  
> Updated at: 2025-04-27 17:41:35 UTC  
> Closed at: 2025-04-27 17:41:35 UTC  
> Url: https://github.com/boostorg/coroutine/pull/66  

replace  
	current_exception()  
with  
	std::current_exception()  
  
and  
 #include <exception>  
  
to bring the symbol into scope.  
  
References:  
  
https://en.cppreference.com/w/cpp/error/current_exception

---
