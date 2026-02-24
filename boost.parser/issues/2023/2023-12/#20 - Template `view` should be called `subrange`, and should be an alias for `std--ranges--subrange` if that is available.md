# #20 - Template `view` should be called `subrange`, and should be an alias for `std::ranges::subrange` if that is available [Closed]

> Username: tzlaine  
> Created at: 2023-12-02 22:14:41 UTC  
> Updated at: 2023-12-04 01:24:00 UTC  
> Closed at: 2023-12-04 01:24:00 UTC  
> Url: https://github.com/boostorg/parser/issues/20  



---

## Comment 1

> Username: tzlaine  
> Created at: 2023-12-03 22:55:44 UTC  
> Url: https://github.com/boostorg/parser/issues/20#issuecomment-1837628043  

It's inconvenient to to an actual alias for `std::ranges::subrange`, given Clang's lake of support for alias CTAD.
