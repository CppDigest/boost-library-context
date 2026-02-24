# #29 - request: tuple_slice [Open]

> Username: HDembinski  
> Created at: 2019-01-07 19:56:14 UTC  
> Updated at: 2020-05-25 15:19:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/29  

I am manipulating function arguments in boost.histogram, which I pass around as `std::tuple`. I found that I need a `tuple_slice` function, something which strips off some elements from a tuple of values from the front and/or back. I use this implementation:  
  
```  
template <class... Ns>  
struct tuple_slice_impl {  
  template <typename T>  
  static decltype(auto) apply(const T& t) {  
    return std::forward_as_tuple(std::get<Ns::value>(t)...);  
  }  
};  
  
template <std::size_t Offset, std::size_t N, typename T>  
decltype(auto) tuple_slide(const T& t) {  
  using LN = mp11::mp_iota_c<N>;  
  using OffsetAdder = mp11::mp_bind_front<mp11::mp_plus, mp11::mp_size_t<Offset>>;  
  using LN2 = mp11::mp_transform_q<OffsetAdder, LN>;  
  return mp11::mp_rename<LN2, tuple_slice_impl>::apply(t);  
}  
```  
  
Basically, I generate an integer sequence of indices of the elements that should remain in the slice and then use a parameter pack expansion of `std::get`s.  
  
Usage:  
```  
auto tup1 = std::make_tuple(1, "foo", 3.0);  
auto tup2 = tuple_slice<0, 2>(tup1); // == std::make_tuple(1, "foo");  
auto tup3 = tuple_slice<1, 2>(tup1); // == std::make_tuple("foo", 3.0);  
// and so on  
```  
  
I think this is a useful addition to Mp11. The implementation is not trivial and it seems like a common tuple manipulation.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-01-07 20:44:36 UTC  
> Url: https://github.com/boostorg/mp11/issues/29#issuecomment-452075453  

A simpler implementation:  
```  
template <std::size_t Offset, class T, std::size_t... I>  
decltype(auto) tuple_slice_impl(T&& t, index_sequence<I...>) {  
    return std::forward_as_tuple(std::get<(I + Offset)>(std::forward<T>(t))...);  
}  
  
template <std::size_t I, std::size_t N, class T>  
decltype(auto) tuple_slice(T&& t) {  
    return tuple_slice_impl<I>(std::forward<T>(t), make_index_sequence<N>{});  
}  
```  
`decltype(auto)` needs to be replaced with an explicitly computed type in C++11.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-01-07 20:56:17 UTC  
> Url: https://github.com/boostorg/mp11/issues/29#issuecomment-452078720  

Related SO questions with similar implementations  
https://stackoverflow.com/questions/8569567/get-part-of-stdtuple  
https://stackoverflow.com/questions/17854219/creating-a-sub-tuple-starting-from-a-stdtuplesome-types

---

## Comment 3

> Username: pdimov  
> Created at: 2019-01-08 04:31:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/29#issuecomment-452171906  

Is this supposed to return a "view" into the original tuple, instead of a copy?

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-01-08 15:51:52 UTC  
> Url: https://github.com/boostorg/mp11/issues/29#issuecomment-452348125  

Yes, I think a view is appropriate.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-05-24 14:54:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/29#issuecomment-633242990  

If we return to this one, while we're at it, a view makes sense, but in this case I'd prefer if the function has `view` in the name. How about `tuple_subview` after [`static_string::subview`](https://www.boost.org/doc/libs/1_73_0/libs/static_string/doc/html/static_string/ref/boost__static_strings__basic_static_string/subview.html) (as opposed to `substr`)?

---

## Comment 6

> Username: HDembinski  
> Created at: 2020-05-25 15:19:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/29#issuecomment-633619161  

Agreed, I was thinking along the same lines recently.
