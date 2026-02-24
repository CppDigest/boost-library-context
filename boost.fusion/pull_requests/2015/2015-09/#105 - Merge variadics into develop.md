# #105 Merge variadics into develop. [Merged]

> Username: Flast  
> Created at: 2015-09-28 23:31:21 UTC  
> Updated at: 2016-05-23 15:28:43 UTC  
> Merged at: 2015-10-22 07:40:04 UTC  
> Closed at: 2015-10-22 07:40:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/105  

I think it is good time to merge variadics into develop and sieve regressions.

---

## Comment 1

> Username: vtnerd  
> Created_at: 2015-10-05 21:44:44 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41201909  

Why is there a specialization for zero elements? Seems like the `N` version below would work. Could any rationale be added as a comment here, to prevent the temptation to remove it?

---

## Comment 2

> Username: vtnerd  
> Created_at: 2015-10-05 21:45:17 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41201972  

- Requiring variadics and r-value references for `fusion::set` cpp11 mode seems like a low barrier. @djowel ? I ask because `fusion::vector` requires r-value references for cpp11 mode and is used for storage. So if a compiler has variadic support, but not r-value reference support, `fusion::set` will use the `fusion::vector` cpp03 for data storage.  
- Should these first two contain an `#else` block? The universal reference will cover both cases.  
- Does `template<typename Sequence> set(Sequence const& rhs)` and `set(detail::call_param<T>::type ...args)` result in ambiguity if there is a nested sequence (`fusion::set<fusion::vector<int>>`)?  
- Does `template<typename Sequence> set(Sequence&& rhs)` and `template<typename ...U> set(U&& ...args)` result in ambiguity if one argument is provided to the constructor? I think the latter should cover both cases.  
- Should the assignment operators use `#else` blocks? The universal reference covers both.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2015-10-05 21:54:39 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41202886  

MSVC has trouble parsing "fake expressions" resulting from expanding empty packs. That might be the reason.

---

## Comment 4

> Username: vtnerd  
> Created_at: 2015-10-05 23:18:04 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41210103  

I think it would be easier to have at_impl be a static function, something like:  
  
``` c++  
template<std::size_t index, typename J>  
static BOOST_CXX14_CONSTEXPR BOOST_FUSION_GPU_ENABLED const J& at_impl(const store<index, J>& value)  
{  
    return value.get();  
}  
```  
  
That way the compiler does a search against the inheritance tree of `vector_data`, and deduces `J` from that search. I just tested this:  
  
``` c++  
#include <iostream>  
#include <utility>  
  
template<std::size_t index, typename T>  
struct store  
{  
        store(const T& t) : value(t) {}  
        T value;  
};  
  
template<typename Indices, typename... T>  
struct vector_base;  
  
template<std::size_t ...Indices, typename ...T>  
struct vector_base<std::integer_sequence<std::size_t, Indices...>, T...>  
        : store<Indices, T>...  
{  
        vector_base(const T&... t)  
                : store<Indices, T>(t)...  
        {  
        }  
};  
  
template<typename... T>  
struct vector : vector_base<std::make_index_sequence<sizeof...(T)>, T...>  
{  
        using base = vector_base<std::make_index_sequence<sizeof...(T)>, T...>;  
  
        vector(const T&... t)  
                : base(t...)  
        {  
        }  
};  
  
template<std::size_t index, typename T>  
const T& get(const store<index, T>& value)  
{  
        return value.value;  
}  
  
  
int main()  
{  
        using my_vector = vector<int, float, int, float>;  
  
        my_vector test(10, 0.5f, 100, 1.5f);  
        std::cout << get<2>(test) << std::endl;  
        return 0;  
}  
```  
  
in clang 3.5 and it works. Hopefully MSVC doesn't strike again here, I _think_ this would be a lot quicker at compile time. It would remove the linear instantation sequence (via `value_at_impl`), and that metafunction could be updated to use this static function + decltype.

---

## Comment 5

> Username: vtnerd  
> Created_at: 2015-10-05 23:21:55 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41210367  

`vector` and `tuple` use perfect forwarding for their make functions.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2015-10-06 00:11:53 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41213819  

After providing implementations for `integer_sequence`, and `make_index_sequence`, I got this to compile and run correctly with MSVC 2013.

---

## Comment 7

> Username: djowel  
> Created_at: 2015-10-06 00:30:40 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41215000  

+1 on requiring variadics and r-value references for fusion::set cpp11

---

## Comment 8

> Username: djowel  
> Created_at: 2015-10-06 00:35:18 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41215278  

Again +1

---

## Comment 9

> Username: djowel  
> Created_at: 2015-10-06 00:40:09 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41215580  

Don't we have an optimized implementation of  `integer_sequence`, and `make_index_sequence` in boost? The CT optimization strategies is quite subtle.  
  
(Nit: whatever you do, please follow the convention T const& instead of const T& for consistency.)

---

## Comment 10

> Username: K-ballo  
> Created_at: 2015-10-06 00:45:01 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41215892  

Not in Boost, but Fusion has one as an implementation detail.

---

## Comment 11

> Username: vtnerd  
> Created_at: 2015-10-06 01:05:59 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41217059  

`integer_sequence` and `make_index_sequence` were only used in my rough testing version of `fusion::vector`. They are not needed to implement the proposed change for accessing a specific element or type in the `fusion::vector`.  
  
The current code uses `value_at_impl` to determine the type at a specific index, and then casts to the base class `store<Index, Type>` to get that element. `value_at_impl` is scanning from the front of the `fusion::vector` until it reaches the desired index, and then returns the type at the index. However, template-argument deduction to compute `Type` on the base class `store<Index, Type>` should be sufficient. Apparently, @K-ballo already did this quite some time ago, so he might be able to speak to any possible efficiency gains.

---

## Comment 12

> Username: K-ballo  
> Created_at: 2015-10-06 01:20:37 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41217828  

Linear recursive instantiations are the worst. They generate N superlinear symbol names, instead of the single one an overload resolution on bases would.

---

## Comment 13

> Username: Flast  
> Created_at: 2015-10-06 01:22:10 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41217898  

Should it be container-wide requirements (i.e. all fusion containers require r-value references)?

---

## Comment 14

> Username: djowel  
> Created_at: 2015-10-06 01:23:35 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41217950  

I think so. at least for cpp11+

---

## Comment 15

> Username: Flast  
> Created_at: 2015-10-06 02:20:50 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41220449  

I think it will requires `decltype` to implement O(1) `vector.at_impl` because we should provides `fusion::at<S,N>::type` for compatibility. Is it OK?

---

## Comment 16

> Username: K-ballo  
> Created_at: 2015-10-06 13:14:31 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41261272  

Every compiler with variadic templates support comes with `decltype` already.

---

## Comment 17

> Username: Flast  
> Created_at: 2015-10-07 08:40:05 UTC  
> Updated_at: 2015-10-21 01:43:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#discussion_r41364746  

Great! OK, I will do this.

---

## Comment 18

> Username: Flast  
> Created_at: 2015-10-21 09:19:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#issuecomment-149831833  

Any other suggestions?

---

## Comment 19

> Username: djowel  
> Created_at: 2015-10-21 21:09:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/105#issuecomment-150025004  

Looks good to me.

---
