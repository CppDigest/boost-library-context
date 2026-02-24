# #211 - Consider implementing a lexicographical comparison algorithm like `std::equal` [Open]

> Username: ldionne  
> Created at: 2015-11-20 16:39:01 UTC  
> Updated at: 2017-07-24 17:31:08 UTC  
> Url: https://github.com/boostorg/hana/issues/211  

We have `hana::equal` to compare arbitrary objects and sequences, and we have `hana::lexicographical_compare` which is like `std::lexicographical_compare` (hence for `Iterable`s only). It might be nice to have some generic algorithm that does like `std::equal` (compare for equality of `Iterable`s), which we could then use to implement `hana::equal` for sequences.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-07-24 16:52:38 UTC  
> Url: https://github.com/boostorg/hana/issues/211#issuecomment-317484486  

The feature is trivial to implement, and in fact we already have [an internal helper](https://github.com/boostorg/hana/blob/cae3fb3fb/include/boost/hana/equal.hpp#L134-L164) that does that. The only challenge is how to name it, since we can't reuse `hana::equal`. Any suggestions?

---

## Comment 2

> Username: ricejasonf  
> Created at: 2017-07-24 17:11:16 UTC  
> Url: https://github.com/boostorg/hana/issues/211#issuecomment-317490188  

`lexicographical_equal` maybe?  
  
It seems like what this issue is requesting already exists but you are saying it should be public facing. Is that right?

---

## Comment 3

> Username: ldionne  
> Created at: 2017-07-24 17:11:59 UTC  
> Url: https://github.com/boostorg/hana/issues/211#issuecomment-317490437  

Yes, exactly. And the only barrier for making it public facing is to find a reasonable name for it.

---

## Comment 4

> Username: ricejasonf  
> Created at: 2017-07-24 17:26:59 UTC  
> Url: https://github.com/boostorg/hana/issues/211#issuecomment-317494784  

Would it be identical to calling `hana::lexicographical_compare` with the pred as `hana::equal`?

---

## Comment 5

> Username: ldionne  
> Created at: 2017-07-24 17:31:08 UTC  
> Url: https://github.com/boostorg/hana/issues/211#issuecomment-317495978  

No, that would not work. That would not only violate the requirement that the predicate is a total ordering as defined by the `Orderable` concept, but also it wouldn't work in terms of implementation, because it would short-circuit at the first equal elements.
