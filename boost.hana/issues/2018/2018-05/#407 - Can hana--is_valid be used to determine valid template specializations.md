# #407 - Can hana::is_valid be used to determine valid template specializations? [Closed]

> Username: invexed  
> Created at: 2018-05-31 08:58:06 UTC  
> Updated at: 2018-06-05 16:02:16 UTC  
> Closed at: 2018-06-05 16:02:16 UTC  
> Url: https://github.com/boostorg/hana/issues/407  

In essence what I'm trying to do is evaluate whether or not generic type trait structs take one or two template arguments:  
  
```c++  
template<template<typename...> typename Trait>  
constexpr auto is_unary_trait{  
    hana::is_valid([]() -> Trait<void> {})  
};  
  
template<template<typename...> typename Trait>  
constexpr auto is_binary_trait{  
    hana::is_valid([]() -> Trait<void, void> {})  
};  
```  
This doesn't work because the compiler complains that the wrong number of template arguments are being passed to one of these functions. Is something like this possible with `hana::is_valid`?

---

## Comment 1

> Username: ldionne  
> Created at: 2018-06-04 21:51:53 UTC  
> Url: https://github.com/boostorg/hana/issues/407#issuecomment-394511980  

It ought to be possible, however [CWG 1430](http://www.open-std.org/Jtc1/sc22/wg21/docs/cwg_active.html#1430) is getting in the way. I could technically reimplement `hana::template_` like this:  
  
```c++  
template <template <typename ...> class F>  
struct template_t {  
    template <typename ...T>  
    struct apply {  
        using type = F<T...>;  
    };  
  
    template <typename ...T>  
    constexpr hana::type<F<typename T::type...>>  
    operator()(T const& ...) const { return {}; }  
};  
```  
  
to make sure it is SFINAE-friendly, and then you could use the following code to check what you want:  
  
```c++  
template <typename T, typename U>  
struct Foo;  
  
template <typename T>  
struct Bar;  
  
auto is_binary_template = hana::is_valid([](auto trait) -> decltype(  
  trait(hana::type_c<void>, hana::type_c<void>)  
) { });  
  
static_assert(is_binary_template(hana::template_<Foo>));  
static_assert(!is_binary_template(hana::template_<Bar>));  
```  
  
([live example](https://wandbox.org/permlink/yyI9VBz733Dcl8y8))  
  
However, this does not work at the moment for template aliases that expand into a non-variadic template because of CWG 1430. I'm looking into it.

---

## Comment 2

> Username: ldionne  
> Created at: 2018-06-04 22:17:51 UTC  
> Url: https://github.com/boostorg/hana/issues/407#issuecomment-394518394  

I think I figured out a way to solve it.

---

## Comment 3

> Username: invexed  
> Created at: 2018-06-05 16:02:16 UTC  
> Url: https://github.com/boostorg/hana/issues/407#issuecomment-394765841  

Fantastic! Thanks again for your continued hard work on Hana.
