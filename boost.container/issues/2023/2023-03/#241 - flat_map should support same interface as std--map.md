# #241 - flat_map should support same interface as std::map [Closed]

> Username: J-Richter  
> Created at: 2023-03-29 12:59:40 UTC  
> Updated at: 2024-06-28 12:04:06 UTC  
> Closed at: 2024-06-28 12:04:06 UTC  
> Url: https://github.com/boostorg/container/issues/241  

std::map has a transparent erase function. flat_map should support the same interface.   
  
This means flat_map can be used with fewer allocations like so:  
  
    boost::container::flat_map<std::string,int,std::less<void>> v;  
    v.erase( "test"sv );  
  
flat_map should also provide the insert_or_assign function.
