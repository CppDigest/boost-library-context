# #299 use fold expressions in or_parser and seq_parser [Merged]

> Username: andreasbuhr  
> Created at: 2025-12-03 07:56:28 UTC  
> Updated at: 2025-12-06 05:48:02 UTC  
> Merged at: 2025-12-06 05:48:02 UTC  
> Closed at: 2025-12-06 05:48:02 UTC  
> Url: https://github.com/boostorg/parser/pull/299  

In the code, there was a TODO not to use fold expressions instead of detail::hl::for_each.  
  
I do not know why one would prefer the fold expression, but as there is the TODO note I just did it.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-12-06 05:47:57 UTC  
> Url: https://github.com/boostorg/parser/pull/299#issuecomment-3619598773  

How nice!  Thanks, Andreas.  The only reason for wanting to do this is that it's a shallower template instantiation stack when things blow up.  That's why it was still a TODO -- it was only a nice-to-have.

---
