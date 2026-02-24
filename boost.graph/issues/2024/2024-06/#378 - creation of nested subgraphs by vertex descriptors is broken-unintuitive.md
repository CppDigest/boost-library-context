# #378 - creation of nested subgraphs by vertex descriptors is broken/unintuitive [Open]

> Username: reuther-genisys  
> Created at: 2024-06-10 13:27:44 UTC  
> Updated at: 2025-08-18 03:54:21 UTC  
> Url: https://github.com/boostorg/graph/issues/378  

When using `subgraph::create_subgraph(VertexIterator first, VertexIterator last)`, the vertex descriptors are interpreted as vertex descriptors into the root graph. (`add_vertex` expects the given descriptor to be a root graph descriptor).  
  
My expectation is that the vertex descriptors should be interpreted as descriptors for the subgraph on which `create_subgraph` is called on. This way you can get a subgraph object, do some vertex selection logic on it and pass the set of selected vertices to `create_subgraph` without ever needing to worry where the graph is situated in the subgraph tree.  
  
The fix is to replace line 197 `add_vertex(*first, *m_children.back());` with `add_vertex(local_to_global(*first), *m_children.back());`.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2024-06-18 00:11:07 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-2174664104  

I'm a little nervous about changing behaviour in a way that might break existing code, although I agree that the current implementation does not sound ideal.  
  
Do you mind giving a more full example that demonstrates how unintuitive the current implementation is?

---

## Comment 2

> Username: reuther-genisys  
> Created at: 2024-06-19 12:29:10 UTC  
> Updated at: 2024-06-19 12:38:19 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-2178583601  

The current approach breaks for all algorithms where a graph is recursively split into smaller subgraphs.  
  
My application is the decomposition of a cycle-free graph into a complete set of longest-possible traversal paths. (Complete here means that every vertex belongs to exactly one resulting traversal path). The approach is recursive and looks like this:  
  
```  
using Graph = boost::subgraph<G>;  
using VertexD = boost::graph_traits<Graph>::vertex_descriptor;  
  
void recurse(Graph graph, std::vector<std::vector<Object>>& paths)  
{  
  std::vector<std::set<VertexD> > components = ComponentDecomposition(); // essentially connected_components(), sorting the result into sets for ease of use  
  
  for (auto&& component: components)  
  {  
    Graph componentGraph = graph.create_subgraph(component.begin(), component.end());   
  
    std::vector<VertexD> path = GetLongestPath(componentGraph);  // essentially bfs starting at each vertex of degree 1  
  
    paths.push_back(GetPathObjects(path)); // extract the Object properties from the path vertices  
  
    std::set<VertexD> remainingVertices(vertices(componentGraph).first, vertices(componentGraph).second);  
    for (auto&& v: path)  
       remainingVertices.erase(v);  
  
    recurse(componentGraph.create_subgraph(remainingVertices.begin(), remainingVertices.end()), paths);  
}  
```  
  
Both `create_subgraph` calls here operate on vertex descriptors obtained from the present graph. With the current behavior, this code breaks immediately on the second `create_subgraph` call..  
  
If you do not want to change behavior, the documentation should at least be updated (for all versions!) to reflect the behavior:   
  
> When calling `create_subgraph`, transform all vertex descriptors in the iterator range to global descriptors using `local_to_global` first.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2024-07-16 00:21:51 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-2229717886  

Let me get this straight in my head with some hierarchical labelling. I'll use **A**, **B**, **C** to denote graphs, _a_, _b_, _c_ to denote vertices from their respective graphs. By definition of sequence, **C** is a subgraph of **B**, **B** is a subgraph of **A**, etc.  
  
When we call `ComponentDecomposition()` that's called on **A**, and so `component` is a list of _a_.   
Then at the top of the loop, we call **A**.create_subgraph(_a_), yielding a **B** (`componentGraph`).  
`remainingVertices` is a _b_ because it comes from **B**.  
It then calls **B**.create_subgraph(_b_) in the call to `recurse`.  
  
Intuitively, yes, it looks like that second call, **B**.create_subgraph(_b_), should just work without transforming local vertex descriptors to global ones, especially as the documentation for `create_subgraph`. Let me look through some more examples and documentation to be sure I haven't missed something.  
  
One thing that concerns me in your algorithm, which is somewhat related, is that it looks like `paths` will contain lists of local vertex descriptors for subgraphs that no longer exist? Or do you convert them to global vertex descriptors in your actual implementation?  
  
Another thing, just a performance hint, but you're probably better of using `std::vector` and the `std::set_` algorithms rather than using `std::set`.

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2024-07-16 00:26:09 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-2229724132  

I'd better take #161 into consideration when I look at this.

---

## Comment 5

> Username: reuther-genisys  
> Created at: 2024-07-31 13:48:06 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-2260571118  

> When we call `ComponentDecomposition()` that's called on **A**, and so `component` is a list of _a_. Then at the top of the loop, we call **A**.create_subgraph(_a_), yielding a **B** (`componentGraph`). `remainingVertices` is a _b_ because it comes from **B**. It then calls **B**.create_subgraph(_b_) in the call to `recurse`.  
>  
> Intuitively, yes, it looks like that second call, **B**.create_subgraph(_b_), should just work without transforming local vertex descriptors to global ones, especially as the documentation for `create_subgraph`. Let me look through some more examples and documentation to be sure I haven't missed something.  
  
Exactly: This is the (first) call that *will* yield wrong results. It can even modify B<sup>*</sup>.  
  
> One thing that concerns me in your algorithm, which is somewhat related, is that it looks like `paths` will contain lists of local vertex descriptors for subgraphs that no longer exist? Or do you convert them to global vertex descriptors in your actual implementation?  
  
I extract vertex properties ("`Object`", by calling `GetPathObjects` <sup>+</sup>) and store these in the resulting paths. However, please consider my example to be mock code ;).  
  
> Another thing, just a performance hint, but you're probably better of using `std::vector` and the `std::set_` algorithms rather than using `std::set`.  
  
Thank you for the hint, I will look into these.  
  
<sup>\* When adding vertices to the newly created subgraph by `add_vertex`, vertices can also be added to the subgraphs parent (subgraph.h:417ff)</sup>  
  
<sup>+ Yes, `GetGraphObjects` should also get `componentGraph` as parameter for this to work. As I said, mock code...</sup>

---

## Comment 6

> Username: reuther-genisys  
> Created at: 2025-04-04 13:20:28 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-2778718663  

Is there an update to this issue?

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2025-07-21 22:53:19 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3099744332  

Sorry mate, looks like I dropped the ball on this one.   
Honestly it's probably best if you can find someone else who has been actively contributing recently and ask if they can help out with an implementation.   
I can still usually review pretty quickly to get things merged.

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2025-07-21 22:55:46 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3099752552  

Wait, you actually have a review open, how embarrassing.

---

## Comment 9

> Username: reuther-genisys  
> Created at: 2025-07-22 07:44:28 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3101476032  

Jup. It's a one-liner.

---

## Comment 10

> Username: jeremy-murphy  
> Created at: 2025-08-13 02:23:35 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3181970743  

I've been thinking about this, and I might still have to have a look at the implementation, but my feeling is that the problem is with the documentation, not the implementation.   
If the create subgraph function ultimately requires global vertices, then I think it's reasonable to require them on input.  
The implementation could perhaps be improved to make global and local vertices different types so that they can't be accidentally used incorrectly.

---

## Comment 11

> Username: jeremy-murphy  
> Created at: 2025-08-13 02:51:25 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3182009043  

Like, with strong index types, a local index would automatically convert to a global index, in the same way that units automatically convert in units libraries.

---

## Comment 12

> Username: reuther-genisys  
> Created at: 2025-08-13 08:55:13 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3182827083  

While documenting the current behavior is a good thing, I feel that as a solution it does not go far enough. It simply nonsensical that the `create_subgraph` method requires vertex descriptors that are *not* from the graph the method is called on.  
  
To me, the entire global/local-descriptor distinction is an implementation detail which should be not relevant to the user of the library. I feel that you are hesitant to change the behavior of the library, but I think that this is a strict improvement and bug fix which should be in.

---

## Comment 13

> Username: jeremy-murphy  
> Created at: 2025-08-14 02:46:03 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3186564843  

I basically agree with you, the current design seems unfortunate, but we also don't know for sure if there was a reason for it.  
  
I am hesitant to make a 'silent' breaking change, i.e. one that changes semantics without breaking compilation, because that kind of breaking change can be an awful experience for the users. We simply don't know how many users might be depending on the existing semantics.  
  
I would rather do a complete redesign of `subgraph` that breaks compilation and forces users to understand the new design.   
Are you interested in taking on this larger project?  
  
One deficit to the strong vertex type that I mentioned earlier is that it won't automatically work in a loop like in your example. It might require either a type-erased vertex type that can store any level of the subgraph hierarchy... but I'm not sure if that even works, I would have to refresh myself on type erasure.

---

## Comment 14

> Username: jeremy-murphy  
> Created at: 2025-08-14 02:57:09 UTC  
> Updated at: 2025-08-14 02:59:07 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3186585242  

Actually, that loop example probably would work using either `std::any` or some wrapper around it and the `any_cast` gets done inside the `subgraph` code.  
  
PS. Ah, except we are still on C++14. There is `boost::any`, so we might have to use that for now.

---

## Comment 15

> Username: reuther-genisys  
> Created at: 2025-08-14 10:00:15 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3187865154  

I understand your point: Silent behavior changes are dangerous. In this case, however, I still hold the current behavior to be a genuine bug that should be fixed: It runs counter to all(!) documentation and sensibility.   
  
Such changes are what change notes are for. (I like to reference https://xkcd.com/1172/ in such cases).  
  
  
A technical overhaul is possible, since the actual class of the vertex descriptor is not specified, only its properties (https://www.boost.org/doc/libs/latest/libs/graph/doc/Graph.html): Default Constructible, Assignable, Equality Comparable. So in principle there stands nothing against replacing the simple index/iterator which is currently used with a more complex class.   
  
One possible implementation might be:   
- vertex descriptors of subgraphs are a map:   
- the key is the pointer to a given subgraph object, the value is the internal vertex descriptor into that subgraph (i.e., vector index, list/set iterator) itself.  
- creating a new subgraph means adding an entry into each vertex descriptors map  
- a vertex is part of a given subgraph if and only if that subgraphs address is inside the map.  
  
However, this has ramifications for runtime and memory consumption and is a much too large project for such a simple change.

---

## Comment 16

> Username: jeremy-murphy  
> Created at: 2025-08-15 23:36:04 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3193003015  

Yeah the map doesn't sound efficient at all. The local vs global state, which is effectively known at compile time just needs to be encoded in the type system.

---

## Comment 17

> Username: jeremy-murphy  
> Created at: 2025-08-18 03:54:20 UTC  
> Url: https://github.com/boostorg/graph/issues/378#issuecomment-3195017759  

I had a quick look at the code, and it's the current implementation could be cleaned up in a few ways related to this. Basically adding a `bool` template parameter to the class and specializing the class implementation on it would lead to less run-time work at the cost of, of course, more compile-time work. `is_root` would become a constant expression and where it is currently used as run-time logic would instead be used to specialize each class implementation. (These are notes to myself or whomever takes on the task.)
