# #833 - Conversion category for optionals [Closed]

> Username: grisumbras  
> Created at: 2023-01-02 11:42:04 UTC  
> Updated at: 2023-08-23 16:47:50 UTC  
> Closed at: 2023-08-23 16:47:50 UTC  
> Url: https://github.com/boostorg/json/issues/833  

#807 introduced a special case for when an struct contains an optional member. But it is special cased for `std::optional`. Having a similar behaviour for `boost::optional` is logical, but implementing it the same way introduces a dependency on Boost.Optional and it's also a bit silly to have several special cases. It appears, we need a special coversion category for optionals. We can look at Boost.Url for ideas on how to implement the check for whether something is an optional.
