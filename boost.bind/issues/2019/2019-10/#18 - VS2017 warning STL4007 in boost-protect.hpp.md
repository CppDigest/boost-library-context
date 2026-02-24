# #18 - VS2017 warning STL4007 in boost/protect.hpp [Open]

> Username: k15tfu  
> Created at: 2019-10-14 13:30:25 UTC  
> Updated at: 2019-10-14 13:30:25 UTC  
> Url: https://github.com/boostorg/bind/issues/18  

boost::protect(std::bind()) warns about result_type which is deprecated since C++17:  
  
```  
2>.../boost/bind/protect.hpp(28): warning C4996: 'std::_Weak_result_type<std::_Is_memfunptr<void (__thiscall jetbrains::controller::pipe_client::* )(jetbrains::controller::pipe_client::client_context_ptr,uint32_t,jetbrains::controller::pipe_client::monitoring_context_ptr)>,void>::result_type': warning STL4007: Many result_type typedefs and all argument_type, first_argument_type, and second_argument_type typedefs are deprecated in C++17.  
```
