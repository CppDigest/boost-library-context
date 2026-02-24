# #366 - integral_constant type conversion and automatic type for UDL [Open]

> Username: TheWisp  
> Created at: 2017-09-08 14:07:11 UTC  
> Updated at: 2018-01-16 17:21:23 UTC  
> Url: https://github.com/boostorg/hana/issues/366  

There is currently no conversion between `integral_constant`s of different base types. It would feel more natural if conversions (and cross-type operators?) are supported, similar to those of the underlying integer types.  
  
  
```  
template<size_t N> f (integral_constant<size_t, N>);// f(integral_constant<int, 42>{}) would compile  
```  
  
It seems to me the compile-time number `N` is the interesting part, rather than the integer width part. Relaxing type constraints generally leads to less verbose code on the users' side, when designing type functions that take or return integral-constants. I assume a common example here would be signed / unsigned conversion for compile-time constant indices.  
  
With cross-type operators and conversions it would also enable designs such as automatic precision for UDL operator""_c. Not that it would make any performance difference at compile time, but when `integral_constant` is used by runtime functions, such as decrypting chars from compile-time encrypted ones, this becomes more convenient than if the type were fixed to `unsigned long long`.  
  
  
```  
integral_constant<char, 'a'> + 1_c //integral_constant<char, 'b'>, not sure if it would compile right now  
  
integral_constant<'a'>//c++17 template<auto> version, what would the base type be?  
  
```  
  
`integral_constant` plays a rather central role for type traits / transformation because it is the type of all type trait values, and what all their interactions converge to (that is, when we follow the design of trapping compile-time values as long as possible, as opposed to those of `std::integral_constant`).   
  
While experimenting stuff, I implemented a rudimentary `integral_constant` that represents my ideas:  
[code](https://gitlab.com/TheWisp/SimpleTemplate/blob/master/integralconstant.h), [tests](https://gitlab.com/TheWisp/SimpleTemplate/blob/master/integralconstanttest.cpp).

---

## Comment 1

> Username: ldionne  
> Created at: 2018-01-01 20:33:59 UTC  
> Url: https://github.com/boostorg/hana/issues/366#issuecomment-354674800  

Is this different from http://open-std.org/JTC1/SC22/WG21/docs/papers/2017/p0827r0.md ? What I could indeed do is add the `constant` described in that paper to Hana when the compiler supports C++17.

---

## Comment 2

> Username: TheWisp  
> Created at: 2018-01-16 17:21:23 UTC  
> Url: https://github.com/boostorg/hana/issues/366#issuecomment-358036793  

It is indeed very similar to the proposal, glad to know it is already been worked on :)  
although when it comes to language level solutions, I imagine there could be a both more fundamental and cleaner way, e.g. treating constexpr arguments the same way as template arguments. If that is possible it eliminates the need of compile time integral constants.
