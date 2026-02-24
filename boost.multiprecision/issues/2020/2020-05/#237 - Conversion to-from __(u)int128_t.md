# #237 - Conversion to/from __(u)int128_t [Closed]

> Username: tdiff  
> Created at: 2020-05-20 19:16:19 UTC  
> Updated at: 2022-01-06 18:24:14 UTC  
> Closed at: 2022-01-06 18:24:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/237  

This is a follow-up ticket after discussion in boost-users mailing list (https://lists.boost.org/boost-users/2020/05/90434.php).  
  
Boost.Multiprecision doc [1] mentions that the type parameter of number::convert_to method should be "fundamental", pointing to the list of standard types ('A number can be converted to any fundamental (built-in) type)'.  
This is different to number::convert_to reference [2], which states that "Type T may be any arithmetic type".  
  
[1] https://www.boost.org/doc/libs/1_73_0/libs/multiprecision/doc/html/boost_multiprecision/tut/conversions.html  
[2] https://www.boost.org/doc/libs/1_73_0/libs/multiprecision/doc/html/boost_multiprecision/ref/number.html  
  
Could you please confirm that __(u)int128_t can be treated as "fundamental" on compilers/platforms which support it?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-26 16:50:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/237#issuecomment-751373826  

Sorry for the long delay in getting to this!!  
  
The correct term I should have used is "arithmetic types + bool".  As an extension, recent releases also support __int128 and __float128 but that in turn is dependent on type_traits detecting these via `is_integral` and `is_floating_point` which I think requires GNU extensions enabled via `-std=gnu++XX`.  
  
I'm wondering if there might be a better way of handling this that would support any arbitrary type that we can categorize via numeric_limits support.
