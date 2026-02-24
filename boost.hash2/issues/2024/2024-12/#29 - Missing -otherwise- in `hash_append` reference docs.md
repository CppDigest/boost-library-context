# #29 - Missing "otherwise" in `hash_append` reference docs [Open]

> Username: Lastique  
> Created at: 2024-12-09 01:36:49 UTC  
> Updated at: 2024-12-09 01:42:55 UTC  
> Url: https://github.com/boostorg/hash2/issues/29  

In [this](https://pdimov.github.io/hash2/doc/html/hash2.html#ref_hash_append_hash_append) section, each item in Effects seems to be an alternative rather than a step in the sequence. I think, in such cases each item should end with "; otherwise". Or better yet, the Effects should start with a preamble like "Performs one of the following actions".

---

## Comment 1

> Username: pdimov  
> Created at: 2024-12-09 01:42:54 UTC  
> Url: https://github.com/boostorg/hash2/issues/29#issuecomment-2526608598  

"Otherwise" implies that they are tried in order, and the first match wins, which isn't currently the case (for the same reason I haven't made `tag_invoke` "win" over the rest.)  
  
I've found that ambiguities are generally better reported, than arbitrarily settled.
