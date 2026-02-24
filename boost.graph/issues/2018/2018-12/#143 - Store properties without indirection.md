# #143 - Store properties without indirection [Open]

> Username: mglisse  
> Created at: 2018-12-16 18:42:24 UTC  
> Updated at: 2022-10-14 05:53:48 UTC  
> Url: https://github.com/boostorg/graph/issues/143  

`stored_edge_property` contains a `std::unique_ptr<Property>` instead of the raw `Property`. In my application, this doubles the memory use of the graph and more than doubles the running time. It comes with this explanation  
  
> // Holding the property by-value causes edge-descriptor  
> // invalidation for add_edge() with EdgeList=vecS. Instead we  
> // hold a pointer to the property. std::auto_ptr is not  
> // a perfect fit for the job, but it is darn close.  
  
I tried to understand and couldn't make sense of what this is supposed to mean. In any case, my property is a (wrapper for a) double, and I strongly doubt that storing a double will invalidate anything. Or do people keep pointers/references to internal properties, do some insertions/removals, then expect the properties are still there? If so, I'd like this expensive feature to be optional please... The comment seems to indicate that this is only needed for vecS but it doesn't seem restricted to vecS (and even for vecS I want to be able to avoid it).

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-10-14 05:53:48 UTC  
> Url: https://github.com/boostorg/graph/issues/143#issuecomment-1278518472  

I'm not familiar with this part of the codebase, but... if you make a pull request, I can help you get it merged in.
