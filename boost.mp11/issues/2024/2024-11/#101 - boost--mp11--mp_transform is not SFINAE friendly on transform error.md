# #101 - boost::mp11::mp_transform is not SFINAE friendly on transform error [Open]

> Username: wanghan02  
> Created at: 2024-11-27 11:13:36 UTC  
> Updated at: 2024-11-27 12:41:28 UTC  
> Url: https://github.com/boostorg/mp11/issues/101  

Example could be found below or on [godbolt](https://godbolt.org/z/rE91a8c1a). boost::mp11::mp_transform is not SFINAE friendly on transform error. It is SFINAE friendly on size mismatch though. It would be very helpful in constraints if it is SFINAE friendly.  
  
```  
template<template<typename...> typename F, typename... List>  
concept has_boost_mp_transform = requires { typename boost::mp11::mp_transform<F, List...>; };  
  
static_assert(!has_boost_mp_transform<std::remove_cvref_t, boost::mp11::mp_list<int&, double const>, std::tuple<short, int const&>>); // invalid transform. Does not compile.  
static_assert(!has_boost_mp_transform<std::common_type_t, boost::mp11::mp_list<char*, float>, boost::mp11::mp_list<int, double>>); // invalid transform result. Does not compile.  
static_assert(!has_boost_mp_transform<std::common_type_t, boost::mp11::mp_list<short, float>, boost::mp11::mp_list<int, double, char>>); // size mismatch. Compiles.  
```  
  
An example implementation for c++20 could be found below or on [godbolt](https://godbolt.org/z/j4svze7M3).  
  
```  
namespace no_adl {  
    template<template<typename...> typename F, typename List0, typename... List>  
    struct mp_transform_impl {};  
  
    template<template<typename...> typename F, template<typename...> typename TList0, typename... List>  
    struct mp_transform_impl<F, TList0<>, List...> {  
        using type = TList0<>;  
    };  
  
    template<template<typename...> typename F, template<typename...> typename TList0, typename T0, typename... T, typename... List> requires  
        requires { typename F<T0, boost::mp11::mp_front<List>...>; } &&  
        requires { typename mp_transform_impl<F, TList0<T...>, boost::mp11::mp_pop_front<List>...>::type; }  
    struct mp_transform_impl<F, TList0<T0, T...>, List...> {  
        using type = boost::mp11::mp_append<  
            TList0<F<T0, boost::mp11::mp_front<List>...>>,  
            typename mp_transform_impl<F, TList0<T...>, boost::mp11::mp_pop_front<List>...>::type  
        >;  
    };  
  
    template<template<typename...> typename F, typename... List>  
        requires boost::mp11::mp_same<boost::mp11::mp_size<List>...>::value && (0<sizeof...(List))  
    struct mp_transform: mp_transform_impl<F, List...> {};  
}  
  
template<template<typename...> typename F, typename... List>  
using mp_transform = typename no_adl::mp_transform<F, List...>::type;  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-11-27 11:30:29 UTC  
> Url: https://github.com/boostorg/mp11/issues/101#issuecomment-2503635455  

C++20 implementations aren't of much help to me; the library supports C++11 and up.

---

## Comment 2

> Username: wanghan02  
> Created at: 2024-11-27 12:25:03 UTC  
> Url: https://github.com/boostorg/mp11/issues/101#issuecomment-2503744663  

> C++20 implementations aren't of much help to me; the library supports C++11 and up.  
  
c++11 version could be found below or on [godbolt](https://godbolt.org/z/GGKbhf64T).  
  
```  
namespace no_adl {  
    template<template<typename...> class F, typename ListList, typename=void>  
    struct mp_transform_impl {};  
  
    template<template<typename...> class F, template<typename...> class TList0, typename... List>  
    struct mp_transform_impl<  
        F,  
        boost::mp11::mp_list<TList0<>, List...>  
    > {  
        using type = TList0<>;  
    };  
  
    template<template<typename...> class F, template<typename...> class TList0, typename T0, typename... T, typename... List>  
    struct mp_transform_impl<  
        F,  
        boost::mp11::mp_list<TList0<T0, T...>, List...>,  
        typename std::enable_if<!std::is_same<  
            boost::mp11::mp_list<  
                F<T0, boost::mp11::mp_front<List>...>,  
                typename mp_transform_impl<F, boost::mp11::mp_list<TList0<T...>, boost::mp11::mp_pop_front<List>...>>::type  
            >,  
            void  
        >::value>::type  
    > {  
        using type = boost::mp11::mp_append<  
            TList0<F<T0, boost::mp11::mp_front<List>...>>,  
            typename mp_transform_impl<F, boost::mp11::mp_list<TList0<T...>, boost::mp11::mp_pop_front<List>...>>::type  
        >;  
    };  
  
    template<template<typename...> class F, typename ListList, typename=void>  
    struct mp_transform {};  
  
    template<template<typename...> class F, typename... List>  
    struct mp_transform<  
        F,  
        boost::mp11::mp_list<List...>,  
        typename std::enable_if<  
            boost::mp11::mp_same<boost::mp11::mp_size<List>...>::value &&  
            0<sizeof...(List)  
        >::type  
    >: mp_transform_impl<F, boost::mp11::mp_list<List...>> {};  
}  
  
template<template<typename...> class F, typename... List>  
using mp_transform = typename no_adl::mp_transform<F, boost::mp11::mp_list<List...>>::type;  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2024-11-27 12:41:26 UTC  
> Url: https://github.com/boostorg/mp11/issues/101#issuecomment-2503779680  

The problem with using the generic implementation is that it's much, much slower than the specialized variants for 1/2/3 lists. I'll see what can be done.
