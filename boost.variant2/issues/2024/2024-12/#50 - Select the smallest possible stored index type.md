# #50 - Select the smallest possible stored index type [Closed]

> Username: Lastique  
> Created at: 2024-12-18 00:06:17 UTC  
> Updated at: 2024-12-22 19:06:14 UTC  
> Closed at: 2024-12-22 19:06:14 UTC  
> Url: https://github.com/boostorg/variant2/issues/50  

It would be more efficient to select the smallest possible stored index type, depending on the number of alternative types `T` that can be stored in the variant. For example, if there are up to 127 or 255 alternatives (depending on whether double storage is used), the stored index type could be as small as `unsigned char`, which is probably the majority of use cases.  
  
This may reduce the size and alignment of the `variant` object, if all the alternative types are also small. One example use case is `result<uint8_t, errc>` or something similar, where `errc` is a small error code enum. Normally, I would expect this result to be just 2 bytes, but the index makes it 8.  
  
Note: The proposal is only regarding the *internally stored* index type. I'm not proposing to change the return type of `index()`, for example.

---

## Comment 1

> Username: libbooze  
> Created at: 2024-12-21 16:17:21 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558164005  

I am supportive of this change.  
Similar logic [exists](https://github.com/boostorg/static_string/blob/bff5cb65a5cb9151aacaf56abe80125e0128f43a/include/boost/static_string/static_string.hpp#L88) in `static_string`, maybe would be nice to move it to some common place, although it is only a few lines.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-12-21 18:27:12 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558196050  

That's not going to be easy to test. :-)  
  
https://github.com/boostorg/variant2/actions/runs/12447423584/job/34750910720  
  
Imagine having a limit of 256 in the year 2025.

---

## Comment 3

> Username: libbooze  
> Created at: 2024-12-21 19:22:14 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558212550  

> That's not going to be easy to test. :-)  
  
You can always adjust some compiler flags I presume, or if you are lazy like me just try to compute last type, e.g.  
`X<N-1>` . In theory it is error prone because you rely on no bugs in test setup, i.e.  
` using V = mp_rename< mp_transform<F, mp_iota_c<N>>, boost::variant2::variant >;`  
but  I think it is fine.   
  
I guess harder test to write will be to test that computation of indexes works fine when you have more than 127 alternatives and something throws since I presume current tests will now use uint8, not uint16.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-12-22 00:48:02 UTC  
> Updated at: 2024-12-22 00:49:39 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558284399  

I suppose, you could replace fold expression with template instantiations in Boost.MP11 on large lists. Something along these lines:  
  
```  
template< std::size_t BasePos, typename V, typename T0, typename... Tail >  
struct mp_find_impl1 :  
  mp_find_impl1< BasePos + 1u, Tail... >  
{  
};  
  
// Found case  
template< std::size_t BasePos, typename V, typename... Tail >  
struct mp_find_impl1< BasePos, V, V, Tail... >  
{  
  using type = mp_size_t< BasePos >;  
};  
  
// Not found case  
template< std::size_t BasePos, typename V, typename T0 >  
struct mp_find_impl1< BasePos, V, V > :  
{  
  using type = mp_size_t< BasePos + 1u >;  
};  
  
// Same as the above, 10x unrolled  
template<  
  std::size_t BasePos,  
  typename V,  
  typename T0, typename T1, typename T2, typename T3, typename T4,  
  typename T5, typename T6, typename T7, typename T8, typename T9,  
  typename... Tail  
>  
struct mp_find_impl10 :  
  std::conditional<  
    (sizeof...(Tail) >= 10u),  
    mp_find_impl10< BasePos + 10u, V, Tail... >,  
    mp_find_impl1< BasePos + 10u, V, Tail... >  
  >::type  
{  
};  
  
// 10 specializations for the "found" cases  
template<  
  std::size_t BasePos,  
  typename V,  
  typename T1, typename T2, typename T3, typename T4,  
  typename T5, typename T6, typename T7, typename T8, typename T9,  
  typename... Tail  
>  
struct mp_find_impl10< BasePos, V, V, T1, T2, T3, T4, T5, T6, T7, T8, T9, Tail... > :  
{  
  using type = mp_size_t< BasePos >;  
};  
  
template<  
  std::size_t BasePos,  
  typename V,  
  typename T0, typename T2, typename T3, typename T4,  
  typename T5, typename T6, typename T7, typename T8, typename T9,  
  typename... Tail  
>  
struct mp_find_impl10< BasePos, V, T0, V, T2, T3, T4, T5, T6, T7, T8, T9, Tail... > :  
{  
  using type = mp_size_t< BasePos + 1u >;  
};  
  
// ...  
  
template< template<typename...> class L, typename... T, typename V >  
struct mp_find_new_impl :  
  std::conditional<  
    (sizeof...(T) > 255u),  
    mp_find_impl10< 0u, V, T... >,  
    mp_find_impl< L<T...>, V > // <- same as the current mp_find_impl  
  >::type  
{  
};  
```

---

## Comment 5

> Username: Lastique  
> Created at: 2024-12-22 00:53:38 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558285412  

That said, the internal size of the index is an implementation detail, so I'm not sure if it needs to be tested that much.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-12-22 01:08:29 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558288465  

If the logic picks `unsigned char` for 257 alternatives due to a bug, how would we know that without testing it?

---

## Comment 7

> Username: libbooze  
> Created at: 2024-12-22 09:22:40 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558390025  

> If the logic picks `unsigned char` for 257 alternatives due to a bug, how would we know that without testing it?  
  
Maybe stamp out the 257 `sizeof` 1 alternatives and check  `sizeof` variant is greater than 258.  
Still this does not test that you did not use signed char for size by accident for smaller values, etc. so I think current tests in branch are nice, unless they take a lot of time on CI.

---

## Comment 8

> Username: Lastique  
> Created at: 2024-12-22 11:42:51 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558426754  

> If the logic picks `unsigned char` for 257 alternatives due to a bug, how would we know that without testing it?  
  
Well, you can't test everything. The same argument can be made for 2^32 + 1 alternatives, for example.  
  
I think, guaranteeing the sufficient index type can be achieved through code organization. That is, make interpretation of the index localized to one place and add a `static_assert` there that verifies that the index type is large enough for all possible index values.  
  
Also, another way to look at the problem is that apparently there currently is an implementation limit up to 256 alternatives, so if a user attempts to specify more the compilation will fail anyway. I'm not sure if you want to lift that limit. If you don't, you could just add a `static_assert(sizeof...(T) < 256)` or similar, and that would eliminate the testing problem.

---

## Comment 9

> Username: pdimov  
> Created at: 2024-12-22 11:55:53 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558430031  

> The same argument can be made for 2^32 + 1 alternatives, for example.  
  
In principle, yes. In practice, the number of alternatives can't realistically exceed 1000, let alone 10,000.  
  
> Also, another way to look at the problem is that apparently there currently is an implementation limit up to 256 alternatives  
  
Where are you getting that? No there isn't.  
  
https://pdimov.github.io/blog/2020/05/13/variants-of-680-types/

---

## Comment 10

> Username: Lastique  
> Created at: 2024-12-22 11:58:27 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558430719  

> Where are you getting that? No there isn't.  
  
But your CI failed for 257 alternatives because of compiler limits, didn't it?

---

## Comment 11

> Username: libbooze  
> Created at: 2024-12-22 12:09:48 UTC  
> Updated at: 2024-12-22 12:10:38 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558434272  

> > Where are you getting that? No there isn't.  
>   
> But your CI failed for 257 alternatives because of compiler limits, didn't it?  
  
afaik there is compiler flag to increase that limit  
https://github.com/boostorg/variant2/commit/639bd0232b4d5926ab9b15a99e04efe27a0ffdb2

---

## Comment 12

> Username: pdimov  
> Created at: 2024-12-22 12:15:20 UTC  
> Url: https://github.com/boostorg/variant2/issues/50#issuecomment-2558435867  

When I wrote that blog post, the Clang developers hadn't yet crippled their fold expressions. You have to uncripple them using `-fbracket-limit`. But I just fixed that on the Mp11 side.  
  
The problem is that it takes a lot of RAM (under MSVC at least), and the CI machines only have something like 6-7.5 (Appveyor) and supposedly 16 on GHA, although it doesn't really feel like 16, more like 8. But I think I got it running.
