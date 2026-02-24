# #257 - Some std::make_index_sequence implementations are O(N) [Closed]

> Username: hpesoj  
> Created at: 2016-03-02 16:36:42 UTC  
> Updated at: 2016-03-03 23:01:49 UTC  
> Closed at: 2016-03-03 23:01:49 UTC  
> Url: https://github.com/boostorg/hana/issues/257  

I am currently using Boost.Hana with Clang 3.7.1 and the GCC standard library. The length of `hana::tuple` is currently limited because `std::make_index_sequence` has O(N) recursion complexity. I have no idea if libc++ has a better implementation, but you could support larger containers with a wider range of standard library implementations if you implemented your own `make_index_sequence` with O(log N) complexity, like this for example:  
  
```  
#include <utility>  
  
template<typename S1, typename S2>  
struct build_index_sequence;  
  
template<typename S1, typename S2>  
using build_index_sequence_t = typename build_index_sequence<S1, S2>::type;  
  
template<  
    std::size_t... Cs1,  
    std::size_t... Cs2  
>  
struct build_index_sequence<  
    std::index_sequence<Cs1...>,  
    std::index_sequence<Cs2...>  
>  
{  
    using type = std::index_sequence<Cs1..., sizeof...(Cs1) + Cs2...>;  
};  
  
template<std::size_t N>  
struct make_index_sequence;  
  
template<std::size_t N>  
using make_index_sequence_t =  
    typename make_index_sequence<N>::type;  
  
template<std::size_t N>  
struct make_index_sequence  
{  
    using type = build_index_sequence_t<  
        make_index_sequence_t<N / 2>,  
        make_index_sequence_t<N - N / 2>  
    >;  
};  
  
template<>  
struct make_index_sequence<1>  
{  
    using type = std::index_sequence<0>;  
};  
  
template<>  
struct make_index_sequence<0>  
{  
    using type = std::index_sequence<>;  
};  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-03-02 17:24:18 UTC  
> Updated at: 2016-03-02 18:20:42 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191335980  

For me `std::make_index_sequence` on libstdc++ explodes at around 250 elements, but with libc++ it works fine until well into the hundred thousands.   
  
Coincidentally, we were just playing around with this in the chat yesterday.  
https://gist.github.com/ricejasonf/8c2b54c182e6038fd0ce

---

## Comment 2

> Username: ricejasonf  
> Created at: 2016-03-02 17:38:23 UTC  
> Updated at: 2016-03-02 17:39:02 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191343534  

I tried the above implementation on that same test. It still explodes on libstdc++ and it doesn't scale as well as the libc++ version. (using clang 3.8)

---

## Comment 3

> Username: hpesoj  
> Created at: 2016-03-02 17:52:24 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191348704  

Sorry, I did mean libc++, not libstdc++.  
  
`std::make_index_sequence` also explodes at around 250 elements for me. The implementation I provided, however, crashes the compiler before it hits any recursion limit. Are you sure you tested it correctly?  
  
As for whether it scales as well as the libc++ version, I wouldn't be surprised if they have a better implementation. I just know that libstdc++ doesn't scale well.

---

## Comment 4

> Username: hpesoj  
> Created at: 2016-03-02 17:57:46 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191350266  

Strange. This compiles fine for me:  
  
```  
using t = make_index_sequence_t<1000000>;  
```

---

## Comment 5

> Username: ricejasonf  
> Created at: 2016-03-02 18:04:32 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191352669  

Okay.. I fixed my test. I was calling `std::make_index_sequence` inside my `filter` function. It is actually pretty close on libc++ and works just fine on libstdc++ (actually faster but it is a different box)  
  
Sorry about that :sweat_smile:

---

## Comment 6

> Username: hpesoj  
> Created at: 2016-03-02 18:51:32 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191369556  

No problem!  
  
I don't know what the performance difference is compared to libc++, but this implementation seems to run a bit faster for high element counts:  
  
```  
#include <utility>  
  
template<typename S1, typename S2>  
struct build_index_sequence;  
  
template<typename S1, typename S2>  
using build_index_sequence_t = typename build_index_sequence<S1, S2>::type;  
  
template<std::size_t... Cs1, std::size_t... Cs2>  
struct build_index_sequence<  
    std::index_sequence<Cs1...>,  
    std::index_sequence<Cs2...>  
>  
{  
    using type = std::index_sequence<  
        Cs1...,  
        sizeof...(Cs1) + Cs1...,  
        2 * sizeof...(Cs1) + Cs2...  
    >;  
};  
  
template<std::size_t N>  
struct make_index_sequence;  
  
template<std::size_t N>  
using make_index_sequence_t = typename make_index_sequence<N>::type;  
  
template<std::size_t N>  
struct make_index_sequence  
{  
    using type = build_index_sequence_t<  
        make_index_sequence_t<N / 2>,  
        make_index_sequence_t<N % 2>  
    >;  
};  
  
template<>  
struct make_index_sequence<1>  
{  
    using type = std::index_sequence<0>;  
};  
  
template<>  
struct make_index_sequence<0>  
{  
    using type = std::index_sequence<>;  
};  
```

---

## Comment 7

> Username: ldionne  
> Created at: 2016-03-03 04:44:05 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191578960  

Wouldn't it be better to submit a bug report to GCC so they improve their implementation? That would fix the problem for Hana and all other libraries using `std::make_index_sequence`, and wouldn't require us to reinvent the wheel. Note that Hana actually used to implement its own `std::make_index_sequence` (and also all of `<type_traits>`). It was decided that it was better to use the standard library, and rely on the fact that standard libraries have quality implementations.  
  
I'd suggest you open a bug report against libstdc++ and link to this issue. If they don't want to move (which won't happen), then we'll see if it's worth adding a workaround in Hana.

---

## Comment 8

> Username: hpesoj  
> Created at: 2016-03-03 06:14:42 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191598174  

I agree. I will file a bug with GCC.

---

## Comment 9

> Username: kris-jusiak  
> Created at: 2016-03-03 10:04:05 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-191690631  

The newest libstdc++ has it fixed -> https://gcc.gnu.org/ml/libstdc++/2015-11/msg00129.html  
However, it still will be much slower than the newest clang builtin `__make_integer_seq`, but, at least, it's not O(N) anymore

---

## Comment 10

> Username: ldionne  
> Created at: 2016-03-03 23:01:49 UTC  
> Url: https://github.com/boostorg/hana/issues/257#issuecomment-192008403  

@krzysztof-jusiak Thanks for the info. I'll close this then.
