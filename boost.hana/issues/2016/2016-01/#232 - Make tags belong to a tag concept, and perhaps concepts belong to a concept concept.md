# #232 - Make tags belong to a tag concept, and perhaps concepts belong to a concept concept [Closed]

> Username: m-j-w  
> Created at: 2016-01-03 19:01:08 UTC  
> Updated at: 2016-01-04 22:01:52 UTC  
> Closed at: 2016-01-04 22:01:38 UTC  
> Url: https://github.com/boostorg/hana/issues/232  

Playing with `hana::set`, I recognized that it is not possible to take every type as a key. Instead it would for example have to be wrapped as `type<int>` because of the equality comparison as mentioned in previous issue #231. One use-case for a map might be the keys being tags and to dispatch accordingly. However, that would also require tags to be wrapped as types, as in `type<constant_tag>`. Also, tags cannot simply be compared for equality.  
  
Thus, I'd like to propose to give tags their own data type by introducing a `hana_tag = tag_tag`, and perhaps adding a concept `Tag`, along with an equality comparator for `tag1 == tag2`.  
  
This would come in handy in particular if users intend to create a whole lot of new tags for their own possibly related or depending data types.  
  
Similarly, I'd like to propose to give concepts their own `Concept` concept and `concept_tag`.  
  
The above would also have another benefit: I'm constantly typing the wrong thing: a type where there should be a value, a concept where there should be a type etc. It's all just looking the same, `type`, `type_c`, ..., or `int_`, `int_c`, ... (partially because everything is in the same namespace)  
Having a `Tag` and `Concept` concept would permit a static_assertion like `static_assert( ! is_concept<T>::value, "I don't want a concept here, stupid !")` and `static_assert( tag_of<T>::value != tag_tag, "What do you want me to do with a Hana tag ???")`.  
  
Ultimately, having both would give everything in Hana a somewhat similar or conceptually identical representation.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-01-04 22:01:38 UTC  
> Url: https://github.com/boostorg/hana/issues/232#issuecomment-168824791  

While I am sympathetic to the idea of making the meta type-system reflexive (by giving a tag to tags themselves), this would break the requirement that `hana::tag_of` be idempotent (see #212).  
  
Regarding concepts, I want to keep them as minimal as possible, because they may very well be replaced by C++17 concepts at some point.  
  
Hence, in the cases where it makes sense to use tags e.g. as keys of a `hana::map`, I would advise simply wrapping them in `hana::type`s, since that is what they really are (C++ types).
