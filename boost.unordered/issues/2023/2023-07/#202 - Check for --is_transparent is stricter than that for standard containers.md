# #202 - Check for ::is_transparent is stricter than that for standard containers [Closed]

> Username: 13steinj  
> Created at: 2023-07-27 17:04:46 UTC  
> Updated at: 2023-08-11 00:21:42 UTC  
> Closed at: 2023-07-28 23:58:35 UTC  
> Url: https://github.com/boostorg/unordered/issues/202  

(...and for similar libraries, but that's a bit beside the point). Came up with maps of string keys, lookup with string views.  
  
This is a bit of an annoying API difference, ex with [std::map](https://en.cppreference.com/w/cpp/container/map/find).  
  
Various `::find` functions only participate in overload resolution if both `Hash::is_transparent` and `Pred::is_transparent`, but the STL only cares about `Pred::is_transparent` (well, `Compare::is_transparent` for the sake of naming used in cppref).  
  
As far as I'm aware, there aren't standard specializations (nor Boost.ContainerHash specializations) for std::hash/boost::hash, which means people have to create their own. I'd argue that slowly grows to become unmaintainable in a larger codebase as everyone creates their own hash struct just to add this typedef.  
  
E: a word

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-07-28 20:26:08 UTC  
> Url: https://github.com/boostorg/unordered/issues/202#issuecomment-1656291380  

The purpose of the transparent hasher and predicate is so that a temporary Key type isn't materialized when doing lookup. The `is_transparent` typedefs are used to denote that they'll work with a generic type so construction is delayed.  
  
Realistically, an implementation can't expose heterogenous lookup without requiring both of those typedefs exist because when doing lookup for a hash table, we need a hash value to find the bucket and then we need the key equal to search all the collisions in the table for the provided key.  
  
More over, we aim for standards compliance and the standard mandates an implementation checking both typedefs:  
> The member function templates find, count, equal_range, contains, extract, erase, and bucket shall not participate in overload resolution unless the qualified-ids Pred​::​is_transparent and Hash​::​is_transparent are both valid and denote types ([temp.deduct]). Additionally, the member function templates extract and erase shall not participate in overload resolution if is_convertible_v<K&&, iterator> || is_convertible_v<K&&, const_iterator> is true, where K is the type substituted as the first template argument.  
  
https://eel.is/c++draft/unord.req#general-247  
  
Do you have an example of an STL implementation behaving differently than us here?

---

## Comment 2

> Username: 13steinj  
> Created at: 2023-07-28 23:58:35 UTC  
> Url: https://github.com/boostorg/unordered/issues/202#issuecomment-1656474247  

Oh dear, this is what I get for staying up too late. Apologies I seem to have mixed up the ordered and unordered containers' requirements.

---

## Comment 3

> Username: BurningEnlightenment  
> Created at: 2023-08-10 10:47:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/202#issuecomment-1672991167  

However, I wonder whether `Boost.ContainerHash` ought to provide a `std::string_view` specialization with `is_transparent` defined…

---

## Comment 4

> Username: pdimov  
> Created at: 2023-08-10 11:54:26 UTC  
> Url: https://github.com/boostorg/unordered/issues/202#issuecomment-1673077489  

I think we do?  
  
On Thu, 10 Aug 2023 at 13:47, Henrik Gaßmann ***@***.***>  
wrote:  
  
> However, I wonder whether Boost.ContainerHash ought to provide a  
> std::string_view specialization with is_transparent defined…  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/unordered/issues/202#issuecomment-1672991167>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAXYZDVGRFJYX527LT4WKJ3XUS35VANCNFSM6AAAAAA22MAK7M>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 5

> Username: cmazakas  
> Created at: 2023-08-10 14:30:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/202#issuecomment-1673334675  

Hmm, doesn't seem like we do, unless my example is flawed here  
  
https://godbolt.org/z/xejffTxP9

---

## Comment 6

> Username: BurningEnlightenment  
> Created at: 2023-08-10 15:36:53 UTC  
> Updated at: 2023-08-10 15:37:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/202#issuecomment-1673466668  

> I think we do?  
  
I thought you replaced all boost::hash<> specializations with a primary template in boostorg/container_hash@0737c56fec7e31e8eb56390fde29756d171ca3b3. AFAICT that hasn't changed except for the Dinkumware `basic_string` workaround.  
  
There are some `boost::unordered::hash_is_avalanching` specializations for `std::basic_string_view`s but nothing more:   
https://github.com/boostorg/container_hash/blob/226eb066e949adbf37b220e993d64ecefeeaae99/include/boost/container_hash/hash.hpp#L664-L672

---

## Comment 7

> Username: 13steinj  
> Created at: 2023-08-11 00:21:42 UTC  
> Url: https://github.com/boostorg/unordered/issues/202#issuecomment-1674081047  

Funnily enough this exact conversation happened in the workplace and it devolved into a paper idea to provide a stricter option than the open window that is `is_transparent`.  
  
For reference (and I haven't checked the details); this does not appear to be a problem with abseil's unordered maps (at least on the arbitrary version that was tested from at least a year ago).
