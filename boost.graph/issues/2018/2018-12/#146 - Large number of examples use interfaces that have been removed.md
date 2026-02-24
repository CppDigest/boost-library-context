# #146 - Large number of examples use interfaces that have been removed [Open]

> Username: jzmaddock  
> Created at: 2018-12-16 19:05:02 UTC  
> Updated at: 2018-12-16 19:05:02 UTC  
> Url: https://github.com/boostorg/graph/issues/146  

The following files:  
  
kruskal-telephone.cpp ;  
loops_dfs.cpp ;  
scc.cpp ;  
reachable-loop-head.cpp ;  
cc-internet.cpp ;  
reachable-loop-tail.cpp ;  
prim-telephone.cpp ;  
dfs-parenthesis.cpp ;  
edge_connectivity.cpp ;  
edge-connectivity.cpp ;  
  
All use `GraphvizGraph` and related interfaces which have been removed from the library - actually they are commented out with the note:   
  
```  
  // This interface has not worked for a long time  
```  
  
So either we need to remove these examples altogether, or better, figure out what interface they should be using.  
  
In addition strong_components.cpp calls an old Graphviz API whose meaning has changed - as a result it chokes interpreting a filename as Graphviz data.  Replacing the filename with a std::ifstream just moves the error elsewhere :(
