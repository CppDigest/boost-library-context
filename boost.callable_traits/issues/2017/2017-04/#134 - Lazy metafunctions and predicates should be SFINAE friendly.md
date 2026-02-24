# #134 - Lazy metafunctions and predicates should be SFINAE friendly [Closed]

> Username: ldionne  
> Created at: 2017-04-17 03:24:31 UTC  
> Updated at: 2017-04-22 18:59:56 UTC  
> Closed at: 2017-04-22 18:59:56 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/134  

Taken from https://lists.boost.org/Archives/boost/2017/04/234333.php:  
  
> The documentation explicitly states that the violation of pre-conditions for transformation traits produces a SFINAE-friendly substitution error. While that does seem to be true for their eager `*_t` versions as fair as I could verify, it can't possibly hold true for the lazy versions of transformation traits, which is a direct consequence of the way they are defined, namely   
>   
> ```c++  
> template<typename T>   
> struct trait {   
>     using type = trait_t<T>;   
> };  
> ```  
>   
> This always produces a hard error if the pre-conditions for `trait_t` are violated. One option would be to clearly state in the documentation to what extent transformation traits are SFINAE-friendly, however I deem it surprising that the eager versions should be SFINAE friendly whereas lazy versions are not, therefore I suggest that the definition of lazy transformation traits be amended to guarantee this property as well, possibly by simply explicitly triggering a substitution error like follows   
>   
> ```c++  
> template<typename T, typename = void>   
> struct trait {};   
> template<typename T>   
> struct trait<T, std::void_t<trait_t<T>>> {   
>     using type = trait_t<T>;   
> };  
> ```  
>   
> The above is also true for predicate traits, which define a nested typename type in terms of an unfriendly expression, instead of inheriting it along with the integral constant value. This is less of a problem in this case, since the user is most interested in the nested integral constant value, but still there doesn't seem to be any reason for the nested typename type to not be SFINAE-friendly.   
  
From a user perspective, I find it highly surprising that the lazy traits are not SFINAE friendly, while the aliases are. Typically, a `XYZ_t` trait should just be a shorthand for `typename XYZ<...>::type`, but both should be interchangeable as much as possible.

---

## Comment 1

> Username: badair  
> Created at: 2017-04-22 18:59:56 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/134#issuecomment-296394263  

Fixed via 96264a9d997463d8ea83838e2df11ebf7009afd0
