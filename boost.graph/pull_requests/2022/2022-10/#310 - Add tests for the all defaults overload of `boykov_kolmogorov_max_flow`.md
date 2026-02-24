# #310 Add tests for the all defaults overload of `boykov_kolmogorov_max_flow` [Merged]

> Username: sebrockm  
> Created at: 2022-10-21 22:28:03 UTC  
> Updated at: 2022-10-25 07:56:16 UTC  
> Merged at: 2022-10-24 22:43:53 UTC  
> Closed at: 2022-10-24 22:43:53 UTC  
> Url: https://github.com/boostorg/graph/pull/310  

This was supposed to fix #309, but that one turned out to be not an issue.  
  
However, there were no tests for the all defaults overload of `boykov_kolmogorov_max_flow` which I added. So maybe this PR is still worth being merged.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2022-10-24 00:02:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/310#pullrequestreview-1152400961  

📁 test/boykov_kolmogorov_max_flow_test.cpp

```diff
 274 |+ {
 275 |+     return get(&tVertex::vertex_distance, g);
 276 |+ }
```

> Username: jeremy-murphy  
> Created_at: 2022-10-24 00:02:04 UTC  
> Url: https://github.com/boostorg/graph/pull/310#discussion_r1002795899  

Maybe I'm missing something, but do you actually need all the above code in order to test the all-defaults overload?

> Username: sebrockm  
> Created_at: 2022-10-24 08:26:18 UTC  
> Url: https://github.com/boostorg/graph/pull/310#discussion_r1003028626  

My understanding of this is that the all-defaults overload uses overloads of `get` (e.g `get(vertex_distance, g)`) to obtain all the needed properties from the graph. Also, it uses `property_map<Property, Graph>::type` to determine the return type of those `get` functions. So, if the graph doesn't provide all of these by itself, I have to provide them. E.g. `adjacency_list` with interior properties already provides all of these `get` overloads and `property_map` specializations (see the other added test below which works out of the box once all needed interior properties are provided). However, with bundled properties, or any other kind of custom graph, those overloads and specializations do not exist, so I have to provide them.  
Of course, it's questionable whether one would really want to write all this boiler plate code just to be able to call a handier overload of `boykov_kolmogorov_max_flow`. But it should still work (and it does work).  
  
Btw, in my original attempt I only provided the `get` overloads, but was not aware that I also needed to provide specializations for `property_map`. That's why I opened #309. The lack of tests for the all-defaults overload further strengthened my believe that it is a bug. During my attempt of fixing it, I figured out that I was missing the `property_map` specializations and that there is no bug.  
  
Long story short: Yes, I think all of this boiler plate is necessary, but I'd be happy to be proven wrong.

> Username: jeremy-murphy  
> Created_at: 2022-10-24 22:43:21 UTC  
> Updated_at: 2022-10-24 22:43:22 UTC  
> Url: https://github.com/boostorg/graph/pull/310#discussion_r1003835247  

Thanks, @sebrockm , I appreciate the detailed explanation.


---
