# #85 - Non-const lookup methods (`unordered_set`) [Closed]

> Username: indev29  
> Created at: 2022-02-01 06:44:59 UTC  
> Updated at: 2022-02-02 04:57:45 UTC  
> Closed at: 2022-02-02 04:57:45 UTC  
> Url: https://github.com/boostorg/unordered/issues/85  

```c++  
// lookup  
  
const_iterator find(const key_type&) const;  
  
template <class Key>  
typename boost::enable_if_c<detail::are_transparent<Key, H, P>::value,  
  const_iterator>::type  
find(const Key& k) const;  
  
template <class CompatibleKey, class CompatibleHash,  
  class CompatiblePredicate>  
const_iterator find(CompatibleKey const&, CompatibleHash const&,  
  CompatiblePredicate const&) const;  
```  
  
Why aren't there any non-const lookup methods for `unordered_set`?    
`unordered_map` has them, [Boost](https://www.boost.org/doc/libs/1_78_0/doc/html/boost/unordered_set.html#id-1_3_46_10_1_1_1_43_1_1-bb) and [repo](https://github.com/boostorg/unordered/blob/develop/doc/unordered/unordered_set.adoc) docs also state that they should be a part of the `unordered_set` interface. Not to mention that they are present in `std::unordered_set`.  
  
Am I missing something?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-01 15:29:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/85#issuecomment-1026970339  

Is this causing any problems for you?  
  
For unordered sets, it's [unspecified](https://eel.is/c++draft/unord.req.general#8) whether `iterator` and `const_iterator` are the same type (because they have identical semantics.)  
  
So in the case they are, there's no need to have a dedicated non-const overload, because both overloads return the same thing.

---

## Comment 2

> Username: indev29  
> Created at: 2022-02-02 04:57:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/85#issuecomment-1027577578  

> For unordered sets, it's [unspecified](https://eel.is/c++draft/unord.req.general#8) whether `iterator` and `const_iterator` are the same type (because they have identical semantics.)  
  
You are right, that's what i was missing.   
Thank you for your time.
