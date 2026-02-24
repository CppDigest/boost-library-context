# #63 - add directive `defaulted` [Closed]

> Username: akrzemi1  
> Created at: 2024-01-14 10:16:51 UTC  
> Updated at: 2024-01-14 20:29:59 UTC  
> Closed at: 2024-01-14 20:29:59 UTC  
> Url: https://github.com/boostorg/parser/issues/63  

Please, fee free to close this Issue if you consider it too extravagant.  
  
The solution proposed in https://github.com/tzlaine/parser/issues/60 works fine, but seems inelegant to me, as this is the only place where I have to spell out my attribute type, it doesn't allow me to specify my intent directly, and it does not seem necessary at all. Hence this proposal.  
  
Add directive `defaulted` with the following semantic.  
  
 * `defaulted[p]` always matches,  
 * when `p` matches, this is the final result (in terms of consumed input and the value of the attribute),  
 * when `p` doesn't match the final result is the default-constructed value of type being `p`'s attribute type.  
  
So `defaulted[p]` parses the same as `p | eps` except that we can derive the type of the attribute from `p`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-14 20:29:59 UTC  
> Url: https://github.com/boostorg/parser/issues/63#issuecomment-1891064236  

I think this is too niche.  I find it hard to see `defaulted[p]` as an improvement over `p | bp::attr(foo)`.  They seem very similar, but since `attr` already exists, I don't see the point of duplicating part of its functionality and calling it `defaulted`.
