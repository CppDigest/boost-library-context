# #36 - mp_with_index: error in example in documentation [Closed]

> Username: tobias-loew  
> Created at: 2019-05-31 14:28:50 UTC  
> Updated at: 2019-06-01 10:17:14 UTC  
> Closed at: 2019-06-01 10:16:02 UTC  
> Url: https://github.com/boostorg/mp11/issues/36  

Hi,  
in the example to mp_with_index the entity "I" is a variable (not a type) and a "decltype" has to be added.  
  
std::cout << std::get<decltype(I)>( v ) << std::endl;  
  
(with C++20 we will be able to deduce the type directly in the lambda)  
thanks for the great library!

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-31 14:41:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/36#issuecomment-497733522  

The example works as written for me. `I` is `integral_constant`, which has a `constexpr` conversion to its value type, the value of which is used as the template parameter of `get` (the overload taking an index.)

---

## Comment 2

> Username: tobias-loew  
> Created at: 2019-06-01 10:16:02 UTC  
> Updated at: 2019-06-01 10:17:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/36#issuecomment-497932105  

You're right. Sorry for the effort. It now also works for me.
