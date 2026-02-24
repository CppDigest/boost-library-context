# #468 - Is it OK to specialize *_impl templates for user-defined types? [Closed]

> Username: 60rntogo  
> Created at: 2020-07-14 19:02:22 UTC  
> Updated at: 2020-07-14 20:47:54 UTC  
> Closed at: 2020-07-14 20:46:17 UTC  
> Url: https://github.com/boostorg/hana/issues/468  

I would like to make my custom type satisfy, say, the `Functor` concept. This seems to work if I provide specialization of `transform_impl`, but I couldn't find anything about this in the documentation. Only specializing `accessors_impl` appears to be documented. Is this an officially supported feature?

---

## Comment 1

> Username: ldionne  
> Created at: 2020-07-14 19:53:33 UTC  
> Url: https://github.com/boostorg/hana/issues/468#issuecomment-658380565  

Yes, that's the standard way of doing so. The details of the `xxx_impl` mechanism are documented here: http://boostorg.github.io/hana/index.html#tutorial-core.  
  
When a concept documents a minimal complete definition, it means that for any type to model that concept, the corresponding `xxx_impl` functions need to be defined. For example, for [Functor](http://boostorg.github.io/hana/group__group-Functor.html), it means that either `transform_impl` or `adjust_if_impl` must be defined *and* satisfy the properties documented in the concept.

---

## Comment 2

> Username: ldionne  
> Created at: 2020-07-14 19:54:31 UTC  
> Url: https://github.com/boostorg/hana/issues/468#issuecomment-658380986  

Please close if this answers your question, otherwise I'll try to clarify.

---

## Comment 3

> Username: 60rntogo  
> Created at: 2020-07-14 20:46:17 UTC  
> Updated at: 2020-07-14 20:47:54 UTC  
> Url: https://github.com/boostorg/hana/issues/468#issuecomment-658404517  

OK, thanks. This certainly answers my question, I just didn't look hard enough. I read most of the user manual, but didn't get all the way down to the core section which is my bad. On the other hand, I tried searching the documentation for `transform_impl` and `unpack_impl` and neither returned any hits which sent me down the wrong path of wondering if these things are implementation details.
