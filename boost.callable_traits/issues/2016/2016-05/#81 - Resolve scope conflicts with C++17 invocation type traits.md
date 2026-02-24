# #81 - Resolve scope conflicts with C++17 invocation type traits [Closed]

> Username: badair  
> Created at: 2016-05-15 16:38:58 UTC  
> Updated at: 2016-07-30 22:00:07 UTC  
> Closed at: 2016-07-30 22:00:07 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/81  

See C++17 proposal [N3866](http://open-std.org/JTC1/SC22/WG21/docs/papers/2014/n3866.html). Does `callable_traits::function_type<T>` do the same thing as `typename std::invocation_type<T>::type`?
