# #93 - Provide `lambda` [Closed]

> Username: joaquintides  
> Created at: 2024-05-10 15:35:58 UTC  
> Updated at: 2024-10-14 15:15:16 UTC  
> Closed at: 2024-10-14 15:15:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/93  

Implement `lambda` described as follows:  
  
The _placeholder set_ of a type `T`, denoted PS(`T`), is defined as:  
  
* PS(`T`) = ∅ if `T` is a primitive type or a class or union type not resulting from a template instantiation.  
* PS(`mp_arg<N>`) = {`mp_arg<N>`}.  
* PS(`C<Ts...>`) = ∪{PS(`Ts`)...}, where `C` is a class template without non-type template parameters.  
* PS(`T`) = ∪{PS(`Q`), `Q` is a constituent type of `T`} otherwise.  
  
(Note that not all types have a well-defined placeholder set.)  
  
For `T` with well-defined PS, `lambda<T>` is a quoted metafunction such that `lambda<T>::fn<U...>` is the type resulting from replacing `mp_arg<I>` in the formal expression defining `T` with the `I`-th element of `U...`, 0 ≤ `I` < `sizeof...(U)`.  
  
The following is an incomplete toy prototype showing how `lambda` could be implemented (https://compiler-explorer.com/z/r5Krbsnxj):  
```cpp  
#include <boost/mp11/bind.hpp>  
  
template<typename T>  
struct lambda_impl;  
  
template<typename T>  
using lambda=typename lambda_impl<T>::type;  
  
template<typename T>  
struct lambda_impl  
{  
  using type=T;  
};  
  
#define SPECIALIZE_LAMBDA_IMPL(name,...)                  \  
template<typename T> using make_##name=__VA_ARGS__;       \  
                                                          \  
template<typename T> struct lambda_impl<__VA_ARGS__>      \  
{                                                         \  
  using type=boost::mp11::mp_bind<make_##name,lambda<T>>; \  
};  
  
SPECIALIZE_LAMBDA_IMPL(const, const T)  
SPECIALIZE_LAMBDA_IMPL(volatile, volatile T)  
SPECIALIZE_LAMBDA_IMPL(const_volatile, const volatile T)  
SPECIALIZE_LAMBDA_IMPL(pointer, T*)  
SPECIALIZE_LAMBDA_IMPL(ref, T&)  
  
#undef SPECIALIZE_LAMBDA_IMPL  
  
template<template <typename...> class C,typename... Ts>  
struct lambda_impl<C<Ts...>>  
{  
  using type=boost::mp11::mp_bind<C,lambda<Ts>...>;  
};  
  
#include <map>  
#include <type_traits>  
  
template<typename T>  
struct my_less{};  
  
int main()  
{  
  using type=lambda<std::map<boost::mp11::_1,const boost::mp11::_2*,my_less<boost::mp11::_1>>>::fn<int,char>;  
  static_assert(std::is_same<type,std::map<int,const char*,my_less<int>>>::value,"");  
}  
```
