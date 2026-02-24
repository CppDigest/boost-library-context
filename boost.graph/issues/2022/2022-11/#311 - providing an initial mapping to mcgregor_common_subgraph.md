# #311 - providing an initial mapping to mcgregor_common_subgraph? [Open]

> Username: proteneer  
> Created at: 2022-11-02 19:42:44 UTC  
> Updated at: 2022-11-14 04:35:41 UTC  
> Url: https://github.com/boostorg/graph/issues/311  

Would it be reasonable to extend functionality so that the user can provide a pre-specified mapping (often determined by some heuristic) as a seed? I noticed that the internals initialize with an empty correspondence:  
  
https://github.com/boostorg/graph/blob/e4e12158e78157397254505f6cbae688e3e3174f/include/boost/graph/mcgregor_common_subgraphs.hpp#L414  
  
So presumably it might not be too hard to allow for an explicit map? Main question is I'm not sure if there's an invariant that would forbid this.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-11-14 04:35:41 UTC  
> Url: https://github.com/boostorg/graph/issues/311#issuecomment-1313086422  

I'm not familiar with this algorithm, but in principle what you want sounds reasonable.
