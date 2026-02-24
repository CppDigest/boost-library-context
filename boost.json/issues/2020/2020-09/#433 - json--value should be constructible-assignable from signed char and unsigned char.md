# #433 - json::value should be constructible/assignable from signed char and unsigned char [Closed]

> Username: hadrielk  
> Created at: 2020-09-29 12:18:58 UTC  
> Updated at: 2020-10-04 19:28:42 UTC  
> Closed at: 2020-10-04 19:28:42 UTC  
> Url: https://github.com/boostorg/json/issues/433  

A `signed char` should be handled for a `kind::int64`, and an `unsigned char` should be handled for a `kind::uint64`.  
  
That would also allow the typedefs `int8_t` and `uint8_t` to work out-of-the-box.
