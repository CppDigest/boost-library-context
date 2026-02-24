# #147 - No add_edge in labeled_graph [Open]

> Username: jzmaddock  
> Created at: 2018-12-16 19:06:53 UTC  
> Updated at: 2019-01-26 18:06:52 UTC  
> Url: https://github.com/boostorg/graph/issues/147  

while trying to build the example labeled_graph.cpp I see:  
  
```  
1>d:\data\boost\boost\libs\graph\example\labeled_graph.cpp(61): error C2039: 'add_edge': is not a member of 'boost::labeled_graph<G,size_t,boost::defaultS>'  
1>d:\data\boost\boost\libs\graph\example\labeled_graph.cpp(57): note: see declaration of 'boost::labeled_graph<G,size_t,boost::defaultS>'  
```

---

## Comment 1

> Username: deinst  
> Created at: 2019-01-26 16:06:30 UTC  
> Url: https://github.com/boostorg/graph/issues/147#issuecomment-457842946  

Ok, this is trivial to fix, just change the member `add_edge` to the global `add_edge`.  Unfortunately, that leaves us with a particularly useless fragment of code that compiles.    
  
Right now the interface to labeled_graph is undocumented.  If we make a sane example, we should document the class we are demonstrating.  Do we want to document it?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-01-26 18:06:52 UTC  
> Url: https://github.com/boostorg/graph/issues/147#issuecomment-457852269  

> Right now the interface to labeled_graph is undocumented. If we make a sane example, we should document the class we are demonstrating. Do we want to document it?  
  
Oh :(  
  
Down and down the rabbit hole we go!  
  
Looks like this was something that was never finished, a quick google suggests that folks have found it and are actually using it though, also that it has some serious issues: https://stackoverflow.com/questions/46012477/boost-labeled-graph-doesnt-delete-vertices  
  
(Aside: someone should probably triage the issues languishing on the old Trac for this library)...  
  
So.... I guess if you think you have the time to polish this header off and document it, then more power to you for sure, whether it's the most important thing to fix I'm not sure, looks like something that should be fixed at some point though I guess.
