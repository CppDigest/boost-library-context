# #30 - consider adding the detection stuff to mp11? [Closed]

> Username: ghost  
> Created at: 2019-01-29 02:57:04 UTC  
> Updated at: 2019-01-30 03:58:11 UTC  
> Closed at: 2019-01-29 03:36:02 UTC  
> Url: https://github.com/boostorg/mp11/issues/30  

The detection idiom is quite helpful in writing SFINAE. Would you @pdimov  consider adding them to mp11?   
https://en.cppreference.com/w/cpp/experimental/is_detected  
``` cpp  
template <template <class...> class Op, class... Args>  
using is_detected = mp_valid<Op, Args...>;  
  
template <class Default, template <class...> class Op, class... Args>  
using detected_or_t =  
    mp_eval_if<mp_not<mp_valid<Op, Args...>>, Default, Op, Args...>;  
  
template <template <class...> class Op, class... Args>  
using detected_t = detected_or_t<detail::mp_no_type, Op, Args...>;  
  
template <class Expected, template <class...> class Op, class... Args>  
using is_detected_exact = std::is_same<Expected, detected_t<Op, Args...>>;  
  
template <class To, template <class...> class Op, class... Args>  
using is_detected_convertible =  
    std::is_convertible<detected_t<Op, Args...>, To>;  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-29 03:25:21 UTC  
> Url: https://github.com/boostorg/mp11/issues/30#issuecomment-458393292  

These are already in TypeTraits:  
  
https://www.boost.org/doc/libs/1_69_0/libs/type_traits/doc/html/boost_typetraits/reference/detected.html  
https://www.boost.org/doc/libs/1_69_0/libs/type_traits/doc/html/boost_typetraits/reference/detected_or.html  
https://www.boost.org/doc/libs/1_69_0/libs/type_traits/doc/html/boost_typetraits/reference/is_detected.html  
https://www.boost.org/doc/libs/1_69_0/libs/type_traits/doc/html/boost_typetraits/reference/is_detected_convertible.html  
https://www.boost.org/doc/libs/1_69_0/libs/type_traits/doc/html/boost_typetraits/reference/is_detected_exact.html  
  
I don't think they need to be duplicated here.

---

## Comment 2

> Username: ghost  
> Created at: 2019-01-29 03:36:02 UTC  
> Url: https://github.com/boostorg/mp11/issues/30#issuecomment-458395161  

I didn't get the changes in boost_typetraits, sorry for bothering.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-01-29 13:56:20 UTC  
> Url: https://github.com/boostorg/mp11/issues/30#issuecomment-458548378  

I've actually thought about adding mp_valid_or<D, F, T...> (the equivalent of detected_or_t), and mp_valid_and_true<F, T...> which gives mp_true if F<T...> is valid and true, and mp_false otherwise. But given the overlap, I'm not sure we need them.  
  
Can you outline a few recent uses you had for these traits?

---

## Comment 4

> Username: ghost  
> Created at: 2019-01-30 03:58:10 UTC  
> Url: https://github.com/boostorg/mp11/issues/30#issuecomment-458803135  

I mainly use the detection idiom for:  
 1. dectecting nested type like alloctor_type, value_type,  
 2. dectecting member function or ADL funtion t.data() or data(t).  
  
Recently I wrote a `chunk_holder` to hold any continuous chunk buffer such as `vector` or `string`.  
The detection idiom helped a lot in getting the chunk buffer's memory range.
