# #257 - Incorrect exception documentation for lsb/msb [Closed]

> Username: gcerretani  
> Created at: 2020-07-08 13:16:25 UTC  
> Updated at: 2020-12-30 18:54:31 UTC  
> Closed at: 2020-12-30 18:54:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/257  

According to the html documentation  
  
https://github.com/boostorg/multiprecision/blob/c68ece5b86f139c0df2f322e9f3b558e4a5db809/doc/html/boost_multiprecision/tut/gen_int.html#L110  
  
and  
  
https://github.com/boostorg/multiprecision/blob/c68ece5b86f139c0df2f322e9f3b558e4a5db809/doc/html/boost_multiprecision/tut/gen_int.html#L120  
  
`lsb` and `msb` are supposed to throw "a `std::domain_error` if x <= 0".  
  
On the other hand, lsb and msb are implemented in **integer.hpp**:  
  
https://github.com/boostorg/multiprecision/blob/c68ece5b86f139c0df2f322e9f3b558e4a5db809/include/boost/multiprecision/integer.hpp#L118  
  
https://github.com/boostorg/multiprecision/blob/c68ece5b86f139c0df2f322e9f3b558e4a5db809/include/boost/multiprecision/integer.hpp#L135  
  
and throw `std::range_error`.  
  
These exceptions are differents; furthermore, `std::range_error` inherits from `std::runtime_error`, while `std::domain_error` inherits from `std::logic_error`.

---

## Comment 1

> Username: gcerretani  
> Created at: 2020-07-09 08:09:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/257#issuecomment-655977210  

I think the bug is in the implementation, while the documentation is correct. According to the [cppreference documentation](https://en.cppreference.com/w/cpp/error/domain_error), `std::domain_error` would be more appropriated since it  
  
> defines a type of object to be thrown as exception. It may be used by the implementation to report domain errors, that is, situations where the inputs are outside of the domain on which an operation is defined.  
  
On the other hand, [cppreference states that](https://en.cppreference.com/w/cpp/error/range_error) `std::range_error`  
  
> can be used to report range errors (that is, situations where a result of a computation cannot be represented by the destination type).
