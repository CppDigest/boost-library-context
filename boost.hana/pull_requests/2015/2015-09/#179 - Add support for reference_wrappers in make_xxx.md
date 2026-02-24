# #179 Add support for reference_wrappers in make_xxx [Open]

> Username: ldionne  
> Created at: 2015-09-13 16:36:13 UTC  
> Updated at: 2017-05-18 21:14:58 UTC  
> Url: https://github.com/boostorg/hana/pull/179  

This pull request attempts to gradually add support for `reference_wrapper` in the `make_xxx` family of functions. This is in order to fix #176.  
  
As documented in #176, I decided to go with the "forward declare in the `std::` namespace" approach, because I think this will be far better for users (let's not introduce yet another `reference_wrapper` class). The following containers should support `reference_wrapper`:  
- [x] `tuple`  
- [x] ~~`set`~~  
- [x] ~~`map`~~  
- [x] `pair`  
- [x] `optional`  
- [x] `lazy`  
  
We also need to:  
- [x] update the tutorial so it explains which containers can hold references, and how `reference_wrapper` can be used with `make_xxx` to create a container of references.

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-09-13 17:25:28 UTC  
> Url: https://github.com/boostorg/hana/pull/179#issuecomment-139897272  

Actually, `map` and `set` should probably not support `reference_wrapper`s.  
  
For `map`, this is because `make_map` takes `pair`s anyway, and those can hold references if needed. Also, the first element of each pair in a `map` is the key, which must be compile-time `Comparable`, which in turn means that it will be something like a `hana::type`, `IntegralConstant` or another stateless object 95% of the time.  
  
For `set`, this is because the elements of the set must be compile-time `Comparable`, so the same reasoning applies to them as for the map's elements.

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-09-13 20:43:06 UTC  
> Url: https://github.com/boostorg/hana/pull/179#issuecomment-139915710  

This PR is essentially feature complete, but I'm not sure about it. Actually, I'm not sure at all about the whole "storing references in containers" thing. Indeed, consider the following:  
  
``` c++  
#include <boost/hana/tuple.hpp>  
#include <boost/hana/tail.hpp>  
namespace hana = boost::hana;  
  
int main() {  
    int i = 0, j = 1, k = 2;  
    hana::tuple<int&, int&, int&> xs{i, j, k};  
    hana::tuple<int, int> ys = hana::tail(xs);  
    //          ^^^  ^^^ notice the decay  
}  
```  
  
The problem is that `tail` is essentially implemented as `make_tuple(at_c<1>(xs), at_c<2>(xs), ...)`. But since the elements of `xs` are references (not `reference_wrapper`s), they are decayed and we end up creating a tuple of values. Of course, we could transport the fact that `xs` is holding references, but that would require specifying the return type of `tail` as something like `hana::tuple<tuple_element_t<0, Xs>, ...>`. I see two problems:  
1. Doing this generically is going to add complexity to the `Sequence` concept at least.  
2. We'll completely blow away our compile-time performance because `tuple_element_t` can't be implemented efficiently AFAIK.  
  
Also note that this problem extends to basically any algorithm that returns a new sequence created with `make_xxx(at_c<0>(xs), at_c<1>(xs), ...)`, which is a lot of algorithms.  
  
Hence, documenting that containers can hold references and making this easier by supporting the `reference_wrapper` magic seems like asking for a lot of trouble.

---

## Comment 3

> Username: badair  
> Created_at: 2015-11-24 14:18:12 UTC  
> Url: https://github.com/boostorg/hana/pull/179#issuecomment-159280218  

What if hana::type contained an additional type alias for the exact original template argument, without reference decay? Would this remove the need for reference wrapping? Would it be difficult to preserve this across algorithms?

---

## Comment 4

> Username: ldionne  
> Created_at: 2015-11-24 14:19:58 UTC  
> Url: https://github.com/boostorg/hana/pull/179#issuecomment-159280855  

> What if hana::type contained an additional type alias for the exact original template argument, without reference decay?  
  
`hana::type` does not decay references. More specifically, `std::is_same<hana::type<T>::type, T>::value` is always `true`.

---

## Comment 5

> Username: badair  
> Created_at: 2015-11-24 14:32:42 UTC  
> Updated_at: 2016-02-02 04:18:58 UTC  
> Url: https://github.com/boostorg/hana/pull/179#issuecomment-159284573  

Ok, I just realized the "rationale for stripping references" section of the documentation is about decltype_ specifically. My mistake.

---

## Comment 6

> Username: ldionne  
> Created_at: 2015-11-24 14:34:51 UTC  
> Url: https://github.com/boostorg/hana/pull/179#issuecomment-159285254  

Yes, it's about `decltype_` only. No worries.

---

## Comment 7

> Username: ldionne  
> Created_at: 2016-11-08 18:42:35 UTC  
> Updated_at: 2016-11-08 19:51:15 UTC  
> Url: https://github.com/boostorg/hana/pull/179#issuecomment-259222274  

Giving an explicit example of a use case where this issue comes up, for the purpose of a discussion currently going on at the Issaquah meeting. Consider the following:  
  
``` c++  
#include <tuple>  
  
template <typename Tuple, typename U, std::size_t ...i>  
auto push_back_impl(Tuple&& tuple, U&& u, std::index_sequence<i...>) {  
  // if we use deduction guides with unwrapping of reference_wrappers, this  
  // may end up unwrapping reference wrappers under our feet, in this totally  
  // generic context  
  return std::tuple{std::get<i>(std::forward<Tuple>(tuple))..., std::forward<U>(u)};  
}  
  
template <typename ...T, typename U>  
auto push_back(std::tuple<T...>&& tuple, U&& u) {  
  return push_back_impl(std::move(tuple), std::forward<U>(u),  
                        std::make_index_sequence<sizeof...(T)>{});  
}  
  
template <typename ...T, typename U>  
auto push_back(std::tuple<T...> const& tuple, U&& u) {  
  return push_back_impl(tuple, std::forward<U>(u),  
                        std::make_index_sequence<sizeof...(T)>{});  
}  
  
int main() {  
  int i = 1, j = 2, k = 3;  
  // no deduction guides used, we want a ref_wrapper here.  
  std::tuple<int, std::reference_wrapper<int>, int> tuple{i, std::ref(j), k};  
  auto more = push_back(tuple, 4); // oops, just made a copy of j!  
  // dangling reference to the temporary copy made inside push_back  
  int& dangling = std::get<1>(push_back(tuple, 4));  
}  
```

---
