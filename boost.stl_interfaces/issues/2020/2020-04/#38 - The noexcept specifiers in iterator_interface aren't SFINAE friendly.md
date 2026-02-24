# #38 - The noexcept specifiers in iterator_interface aren't SFINAE friendly [Closed]

> Username: mikezackles  
> Created at: 2020-04-02 17:28:33 UTC  
> Updated at: 2020-04-08 22:43:18 UTC  
> Closed at: 2020-04-08 22:43:18 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/38  

This appears to be a problem when combining such iterators with something like range-v3, which can fail with a hard error when trying to detect the concept for ranges using iterators from this lib. See tzlaine/text#133.
