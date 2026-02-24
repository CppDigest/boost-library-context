# #6 - return std::false_type when an arg to can_invoke_constexpr is not a literal type or is not default constructible [Closed]

> Username: badair  
> Created at: 2016-03-31 07:15:16 UTC  
> Updated at: 2016-04-01 21:39:56 UTC  
> Closed at: 2016-04-01 21:39:56 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/6  

Currently, this causes a compiler error. Instead, we should simply return `std::false_type`
