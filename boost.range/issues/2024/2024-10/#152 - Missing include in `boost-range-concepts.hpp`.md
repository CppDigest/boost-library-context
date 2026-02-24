# #152 - Missing include in `boost/range/concepts.hpp` [Closed]

> Username: gogagum  
> Created at: 2024-10-10 22:07:32 UTC  
> Updated at: 2024-10-10 22:10:33 UTC  
> Closed at: 2024-10-10 22:10:33 UTC  
> Url: https://github.com/boostorg/range/issues/152  

`boost::type_traits::is_integral` was used on line [188](https://github.com/boostorg/range/blob/develop/include/boost/range/concepts.hpp#L188) without including its header.
