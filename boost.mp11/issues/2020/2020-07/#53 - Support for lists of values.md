# #53 - Support for lists of values [Closed]

> Username: bernhardmgruber  
> Created at: 2020-07-28 16:30:53 UTC  
> Updated at: 2023-05-17 03:34:06 UTC  
> Closed at: 2023-05-17 03:13:58 UTC  
> Url: https://github.com/boostorg/mp11/issues/53  

Mp11 is a great library to work on lists of types! However, when it comes to lists of values, one has to work around the values by converting them to types using e.g. `std::integral_constant<int, 2>`, so mp11 can work on lists of these values.  
  
The following code sadly does not compile:  
```  
template <int... Values>  
struct List;  
  
using MyList = List<1, 2, 3>;  
using ShorterList = boost::mp11::mp_pop_front<MyList>;  
```  
The workaround is to wrap the values:  
```  
template <typename... Values>  
struct List;  
  
using MyList = List<std::integral_constant<int, 1>, std::integral_constant<int, 2>, std::integral_constant<int, 3>>;  
using ShorterList = boost::mp11::mp_pop_front<MyList>;  
```  
However, then also the result `ShorterList` has all values wrapped in `std::integral_constant`, which is undesirable. This can be worked around again by unwrapping the list using something along the lines of (untested):  
```  
template<class L>  
struct mp_unwrap_sizes_impl;  
  
template<template<class...> class L, typename... T>  
struct mp_unwrap_sizes_impl<L<T...>> {  
    using type = L<T::value...>;  
};  
  
template<typename L>  
using mp_unwrap_sizes = typename mp_unwrap_sizes_impl<L>::type;  
```  
  
But even better would be direct support for values in boost::mp11. I gave that a quick try and it seemed not too hard. If we add the following specialization for `mp_pop_front_impl` for example:  
  
```  
namespace boost::mp11::detail {  
    template<template<auto...> class L, auto T1, auto... T> // change here  
    struct mp_pop_front_impl<L<T1, T...>> {  
        using type = L<T...>;  
    };  
}  
```  
then the code example at the top compiles successfully. This specialization is a verbatim copy of the original specialization of `mp_pop_front_impl` except the `class` keyword inside the template template parameter and before `T1` and `T` are replaced by `auto`, to change the type template arguments to non-type template arguments.  
  
The addition of such specializations for values adds a lot of value to boost::mp11 with probably small cost. Could you please consider adding such overloads to enable the use of this great library on value lists as well? Thank you very, very much!

---

## Comment 1

> Username: breese  
> Created at: 2020-07-29 14:23:11 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-665695148  

`template<auto>` is a C++17 feature.

---

## Comment 2

> Username: bernhardmgruber  
> Created at: 2020-07-29 15:46:07 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-665743568  

> `template<auto>` is a C++17 feature.  
  
Of which I am fully aware :) However, the proposed specializations nicely extend the existing functionality without altering the C++11 part. I therefore believe the proposed specializations could only be made available when the compiler supports non-type template parameter deduction. That should make users happy before and after C++17.  
  
One could raise the question if boost::mp11 allows to have C++17 features, given it is named *11*. However, I think having a fork with the proposed features and naming it mp17 just does not carry its weight.  
  
What motivates me also in favor of proposing these features for boost::mp11 is that AFAIK boost::mp11 is proposed for standardization (cf. http://wg21.link/P0949), where it would probably be rebased on at least C++20, so non-type template parameter deduction would be available there.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-07-29 17:29:10 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-665798428  

A C++17 requirement isn't a problem for an optional feature, but I think you are severely underestimating the scope of the effort. This basically amounts to doubling the current mp11; even if some portions of the implementation can be shared, tests and docs will double.  
  
That aside, it's also not as simple from a design point of view. Sure, push_back and pop_front are trivial. But even for the simplest primitives such as `mp_first` you're faced with the question how to actually return the result. And then once you get to `mp_transform` and `mp_copy_if`, you need to figure out how to represent the transformation metafunction and the predicate. This will then have repercussions for the quoted metafunctions and `mp_bind*`.  
  
For these reasons the mp11 approach is to just embrace type wrapping and mp_list_c and do all the metaprogramming on type lists, then only extract the values in the final step. Sometimes not even then; if you do `mp_for_each` over the final list, you still want the value wrapped in an integral_constant passed to your lambda, in order to be able to use it in constant expressions (such as f.ex. `get<I>`.)  
  
(P0949 was unfortunately rejected by the committee, so mp11 is probably not going to be standard.)

---

## Comment 4

> Username: ilyapopov  
> Created at: 2020-08-08 13:06:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-670927089  

@pdimov What was rationale for rejection?

---

## Comment 5

> Username: pdimov  
> Created at: 2020-08-08 22:04:18 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-670978714  

The stated reason was (quoting from memory) "no type-based metaprogramming library proposal before the committee is in a near enough ready state for C++20" but this made no sense, because (a) mp11 was the only proposal and (b) it was ready.  
  
I suppose the real reason was that the committee didn't want type-based metaprogramming at all, as there was a competing proposal for constexpr/consteval-based metaprogramming. The latest document that describes it is probably http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1240r1.pdf. This seems to also have stalled recently though.

---

## Comment 6

> Username: rolandschulz  
> Created at: 2021-12-02 22:26:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-985052092  

Given that full support for values would double the size of the library, would it make sense to provide a utility function to turn a list of types into a list of values? One would still need to wrap and unwrap as outlined in the issue description. But it would make unwrapping easy. The utility function would work the same as `mp_rename` but would take as 2nd argument a template taking list of values. It could be implemented as:  
  
```  
namespace detail {  
template<class L, template <auto...>class Y> struct mp_value_list_impl;  
template<template <class...>class L, template <auto...>class Y, class...T> struct mp_value_list_impl<L<T...>, Y> {  
    using type = Y<T::value...>;  
};  
}  
  
template<class L, template <auto...>class Y> using mp_value_list = detail::mp_value_list_impl<L, Y>::type;  
```

---

## Comment 7

> Username: pdimov  
> Created at: 2021-12-02 22:35:01 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-985056836  

That's possible, but the thing is, you rarely need a value list at all. It's always an intermediate step towards something else, usually a pack of values. And it's simpler to just use lists of integral constants in the intermediate steps, then at the end convert to what you actually need. Typically, renaming into a list of values doesn't help at that point, it's just an extra step. You can just unpack `T::value...` directly.  
  
Although if you can give some concrete examples of the utility of this primitive, please do so.

---

## Comment 8

> Username: rolandschulz  
> Created at: 2021-12-02 23:07:18 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-985073880  

I was reading https://twitter.com/seanbax/status/1465712333741703182 on my lunch break. Having the utility allows  
```c++  
template<size_t Rank>   
using dextents = mp_value_list<mp_repeat_c<mp_list_c<size_t, dynamic_extent>, Rank>, extents>;  
```

---

## Comment 9

> Username: pdimov  
> Created at: 2021-12-02 23:20:39 UTC  
> Updated at: 2021-12-03 01:25:42 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-985080793  

Oh `mdspan` `extents`. I don't know whether to :-) or :-(. Yes, that's a reasonable justification, I think.

---

## Comment 10

> Username: pdimov  
> Created at: 2021-12-03 01:25:32 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-985136496  

Interestingly, and this takes us back to the initial proposal in this issue, if `mp_repeat_c` supported lists of values as suggested, this would have been `template<size_t Rank> using dextents = mp_repeat_c<extents<dynamic_extent>, Rank>;` which is kind of cool.

---

## Comment 11

> Username: rolandschulz  
> Created at: 2021-12-03 03:35:07 UTC  
> Updated at: 2021-12-03 03:35:28 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-985187624  

Indeed that would be cool. Initially just I wanted to reply to that tweet: "Why do we need a set of new language features if we can simply write `mp_repeat_c<extents<dynamic_extent>, Rank>`". But I realized that it doesn't actual work.

---

## Comment 12

> Username: pdimov  
> Created at: 2021-12-03 03:45:17 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-985191061  

Increasing the ability of this battle station to produce devastating salvos of one-liners is a very tempting proposition.

---

## Comment 13

> Username: bernhardmgruber  
> Created at: 2021-12-09 12:11:01 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-989795226  

> That's possible, but the thing is, you rarely need a value list at all.  
  
> Oh mdspan extents. I don't know whether to :-) or :-(. Yes, that's a reasonable justification, I think.  
  
My original request is motivated by a similar type, which is a list of integers that describes the dimensions of multidimensional arrays and indexes into these. I also use them for indices into a nested struct. Users are expected to deal with this index list a lot in my API, so it is much nicer if they can write `RecordCoord<2, 4, 3>` or `ArrayExtents<256, 256, 8>` instead of providing a type list of integral constants. These types are later used to compute e.g. sizes for static arrays via a product of the value list. Or to recursively index into nested tuples. Yes, I could expand a pack here and use `Index::value...`. Still, it would put a burden on the client side API to request type lists instead of value lists.  
  
> Given that full support for values would double the size of the library, would it make sense to provide a utility function to turn a list of types into a list of values?  
  
I have exactly this conversion function in my library:  
  
```c++  
template<std::size_t... Coords>  
struct RecordCoord {  
    using List = boost::mp11::mp_list_c<std::size_t, Coords...>;  
};  
  
namespace internal {  
  template<typename FromList, template<auto...> class ToList>  
  struct mp_unwrap_values_into_impl;  
    
  template<template<class...> class FromList, typename... Values, template<auto...> class ToList>  
  struct mp_unwrap_values_into_impl<FromList<Values...>, ToList>  
  {  
      using type = ToList<Values::value...>;  
  };  
    
  template<typename FromList, template<auto...> class ToList>  
  using mp_unwrap_values_into = typename mp_unwrap_values_into_impl<FromList, ToList>::type;  
}  
  
template<typename L>  
using RecordCoordFromList = internal::mp_unwrap_values_into<L, RecordCoord>;  
```  
  
The user is supposed to use `RecordCoord<1, 2, 3>` etc. to describe hierarchical coordinates into nested structs. Internally I use `RecordCoord<1, 2, 3>::List` before working with these using mp11. And I finally turn the result back with: `RecordCoordFromList <TypeList>`.

---

## Comment 14

> Username: rolandschulz  
> Created at: 2021-12-09 19:54:15 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-990193081  

Hopefully we get https://github.com/cplusplus/papers/issues/735. This would allow this without doubling the library-size and also support mixed type and literal lists.

---

## Comment 15

> Username: bernhardmgruber  
> Created at: 2021-12-10 05:44:47 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-990635627  

I have been coming back to this proposal over and over again. It would alleviate many problems with the heterogenity of non-type and template template parameters.

---

## Comment 16

> Username: pdimov  
> Created at: 2023-05-15 11:24:19 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-1547675435  

> bernhardmgruber commented on Dec 10, 2021  
  
Where did 2022 go?

---

## Comment 17

> Username: pdimov  
> Created at: 2023-05-17 03:13:58 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-1550622620  

The following has been implemented on develop:  
  
* Added `mp_value`, `mp_list_v`, `mp_rename_v`, `mp_is_value_list`.  
* Added value list support to the primitives in `<boost/mp11/list.hpp>`.  
* Added value list support to `mp_repeat`, `mp_fill`, `mp_at`, `mp_back`, `mp_take`, `mp_pop_back`, `mp_drop`, `mp_insert`, `mp_erase`.  
  
I think that this is enough for closing this issue, although further suggestions are still welcome.

---

## Comment 18

> Username: bernhardmgruber  
> Created at: 2023-05-17 03:34:06 UTC  
> Url: https://github.com/boostorg/mp11/issues/53#issuecomment-1550638347  

You are amazing! Thank you so much, I will check it out with the next Boost release!
