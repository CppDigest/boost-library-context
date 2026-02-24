# #260 - `hana::reverse_partial` has no default constructor. [Closed]

> Username: ricejasonf  
> Created at: 2016-03-08 19:34:15 UTC  
> Updated at: 2016-06-14 16:59:28 UTC  
> Closed at: 2016-06-14 16:59:28 UTC  
> Url: https://github.com/boostorg/hana/issues/260  

Since `hana::partial` has a default constructor, perhaps it would be consistent to provide one for `hana::reverse_partial` as well.  
  
My use case is partially applied `hana::Metafunction`s. It would also be cool to have partially applied Metafunction's that are themselves `hana::Metafunction`s. It could be implemented with `hana::experimental::types`. Maybe it could be called `hana::partial_mf` or something. I am not sure about the naming.  
  
I can do a PR for one or both, if you are interested.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-03-08 19:51:46 UTC  
> Url: https://github.com/boostorg/hana/issues/260#issuecomment-193940628  

I didn't even know that `hana::partial` was default constructible. IOW, it's probably a product of the implementation but something officially provided. But I can sort of see why this might be useful. If we decide that `hana::partial` should be default-constructible, then of course `hana::reverse_partial` should be too. Can you open a separate issue for `hana::partial` metafunctions?
