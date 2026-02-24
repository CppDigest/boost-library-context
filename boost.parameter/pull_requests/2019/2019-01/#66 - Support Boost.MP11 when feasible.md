# #66  Support Boost.MP11 when feasible [Merged]

> Username: CromwellEnage  
> Created at: 2019-01-16 17:09:38 UTC  
> Updated at: 2019-01-21 16:07:37 UTC  
> Merged at: 2019-01-16 23:44:15 UTC  
> Closed at: 2019-01-16 23:44:15 UTC  
> Url: https://github.com/boostorg/parameter/pull/66  

* Add are_tagged_arguments_mp11 and is_argument_pack_mp11 metafunctions when Boost.MP11 is usable.  
* Predicate requirements can be encoded as Boost.MP11-style quoted metafunctions as well as by MPL binary metafunction classes.  
* Argument packs qualify as Boost.MP11-style lists as well as MPL sequences.  
* Internal components and test programs use Boost.MP11 and C++11 type traits vice MPL and Boost.TypeTraits when Boost.MP11 is usable.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-01-18 04:14:06 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455422057  

You are not supposed to specialize mp11::detail types, sorry.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2019-01-18 04:49:36 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455426820  

Oh.  Are there viable alternatives, or do I just need to revert the portion of the changes that allow the ArgumentPack models to also be MP11 lists?

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-01-18 10:03:00 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455492579  

What is, and what isn't, an mp11 list is well defined and not subject to specializations. `empty_arg_list`, for example, isn't. `arg_list<TaggedArg,Next,EmitsErrors>` is a list of exactly size 3, with elements exactly `TaggedArg`, `Next`, and `EmitsErrors`.

---

## Comment 4

> Username: CromwellEnage  
> Created_at: 2019-01-18 22:01:53 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455702152  

I'm still looking for a workaround.  I'm currently stumped at conceptually being unable to do:  
  
``template <typename ...KeyTaggedArgTuples>``  
``struct arg_list;``  
  
``template <>``  
``struct arg_list<> : public empty_arg_list {};``  
  
``template <typename Key, typename TaggedArg, typename ...Rest>``  
``struct arg_list< mp11::mp_list<Key,TaggedArg>, Rest...> : public arg_list<Rest...> {};``  
  
The key problem lies in inheritance, of course, so it'll take me a while to find a way to not rely on it.

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-01-18 22:17:21 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455705863  

If you have `arg_list<KeyTagged...>`, you don't need inheritance or specialization for the zero-arg case. Assuming that the problem is that you want to do lookup by `Key` and that the inner tuples are indeed `mp_list<Key, TaggedArg>` (or `pair`, or `tuple`, or `arg_tuple`, it doesn't matter), you can then use `mp_map_find` for the lookup.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-01-18 22:18:41 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455706170  

That's assuming that `arg_list` only stores type information. If it has data members, things will be trickier.

---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2019-01-18 22:33:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/parameter/pull/66#pullrequestreview-194308822  

📁 include/boost/parameter/are_tagged_arguments.hpp

```diff
  50 |+ 
  51 |+     template <typename TaggedArg0, typename ...TaggedArgs>
  52 |+     using are_tagged_arguments_mp11 = ::boost::mp11::mp_bool<
```

> Username: pdimov  
> Created_at: 2019-01-18 22:33:43 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#discussion_r249205581  

This shouldn't be needed. The original is usable as-is.

> Username: CromwellEnage  
> Created_at: 2019-01-19 00:45:46 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#discussion_r249224218  

I'll remove that and is_argument_pack_mp11 on the next pull request I submit.

> Username: CromwellEnage  
> Created_at: 2019-01-21 06:50:17 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#discussion_r249338587  

Okay, they're removed.


---

## Comment 8

> Username: pdimov  
> Created_at: 2019-01-18 22:44:23 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455712053  

It does, so you'll need to inherit `arg_list<KeyTagged...>` from `KeyTagged...` and then have  
  
```  
template<class Key, class TaggedArg> arg_tuple<Key, TaggedArg>& get( arg_tuple<Key, TaggedArg>& x ) { return x; }  
```  
  
and `get<Key>(list)` will get you the correct base (which will contain the `TaggedArg` as a data member.)  
  
This is similar to how `std::tuple` is implemented, except there indices are used for the keys. An example tuple implementation is here:  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/detail/tuple.hpp  
  
except it's more complex than we need here because it needs to generate an index sequence, whereas we already have the list of keyed bases, so we can just inherit from it directly.

---

## Comment 9

> Username: pdimov  
> Created_at: 2019-01-18 22:58:35 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455715065  

Something like https://godbolt.org/z/JVywuv

---

## Comment 10

> Username: CromwellEnage  
> Created_at: 2019-01-21 06:24:05 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-455961156  

Thanks for your suggestions, @pdimov.  I'll be using them for a different library I've been working on.  
  
For PR #70, I didn't want to lose the ability to use the bracket operator to index by key and retrieve the corresponding argument, so I figured out a way for `flat_like_arg_list` to inherit from either `arg_list` or `empty_arg_list`, and I made a corresponding `tagged_argument_list_of_1` that inherits from either `tagged_argument` or `tagged_argument_rref`.  
  
I'd be grateful if you could review the new PR to ensure that I'm using MP11 properly this time, at least.

---

## Comment 11

> Username: pdimov  
> Created_at: 2019-01-21 16:07:37 UTC  
> Url: https://github.com/boostorg/parameter/pull/66#issuecomment-456124300  

`operator[]` is trivial to implement, see https://godbolt.org/z/3Qids9

---
