# #303 - Add `is_specialization_of<T, TTemplate>` [Closed]

> Username: vittorioromeo  
> Created at: 2016-10-10 13:52:06 UTC  
> Updated at: 2016-10-11 15:01:32 UTC  
> Closed at: 2016-10-11 12:26:20 UTC  
> Url: https://github.com/boostorg/hana/issues/303  

Would a new _Logical_ utility called `is_specialization_of` be within the scope of hana? I found myself writing it multiple time while writing hana-based code.  
  
`is_specialization_of<T, TTemplate>` would return evaluate to `true_` if `T` is a specialization of `TTemplate`. _Example [(also on wandbox)](http://melpon.org/wandbox/permlink/lPkZevNwhqCmX6pU):_  
  
``` cpp  
static_assert(is_specialization_of<std::vector<int>, std::vector>{});  
static_assert(!is_specialization_of<std::map<int, float>, std::vector>{});  
```  
  
The basic implementation is pretty straightforward:  
  
``` cpp  
template <typename, template <typename...> class>  
struct is_specialization_of : boost::hana::false_  
{  
};  
  
template <template <typename...> class TTemplate, typename... Ts>  
struct is_specialization_of<TTemplate<Ts...>, TTemplate> : boost::hana::true_  
{  
};  
```  
  
If this is of interest I can write a PR, after agreeing on implementation details.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-10-10 14:39:50 UTC  
> Updated at: 2016-10-10 14:40:49 UTC  
> Url: https://github.com/boostorg/hana/issues/303#issuecomment-252640954  

I believe it would be a better addition to some `type_traits` library like `Boost.TypeTraits`. Furthermore, it might be useful to actually provide a generic match functionality, such as  
  
``` c++  
matches<std::vector<int>, std::vector<_>> // true  
matches<std::vector<int>, std::vector<or_<int,float>>> // true  
```  
  
I've seen this being used in (I think) Boost.Proto.

---

## Comment 2

> Username: vittorioromeo  
> Created at: 2016-10-11 12:26:20 UTC  
> Url: https://github.com/boostorg/hana/issues/303#issuecomment-252902546  

Alright - I'll close this issue and investigate Proto/TypeTraits.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-10-11 15:01:32 UTC  
> Url: https://github.com/boostorg/hana/issues/303#issuecomment-252943605  

Just to be clear; I think it's a great idea and in fact I've already implemented something like this at some point. However, Hana's role is to provide sequences and algorithms, and the wrappers necessary to do type-level computations with value syntax. I'd rather not add any new type-level computations per-se.
