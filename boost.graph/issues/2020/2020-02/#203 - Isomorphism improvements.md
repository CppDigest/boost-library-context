# #203 - Isomorphism improvements [Closed]

> Username: jan-grimo  
> Created at: 2020-02-13 12:39:52 UTC  
> Updated at: 2024-04-15 05:45:38 UTC  
> Closed at: 2024-04-15 05:45:38 UTC  
> Url: https://github.com/boostorg/graph/issues/203  

I want to improve the `isomorphism` documentation and discuss some improvements to the algorithm.  
  
- Null vertices can appear in the isomorphism map if single-vertex components exist. This should either be explicitly documented or the algorithm adapted to map them, too.  
- Vertex invariant functors actually require the `AdaptableUnaryFunction` concept, not `UnaryFunction` as stated (`argument_type` and `result_type` typedefs)  
- In a named parameter call to `isomorphism`, the second vertex invariant functor additionally requires a `max()` member function as an alternative if `vertex_max_invariant` is not passed. ~~This is undocumented.~~ I think both `vertex_max_invariant` and `max()` here are misnomers, as it is demanded that they are upper **exclusive** bounds on the vertex invariant values (i.e. number of different values, not maximum values). Any added documentation of `max()` should have this information too, though.  
- Both `vertex_max_invariant` and `vertex_invariant2::max()` are unnecessary: `test_isomorphism` immediately evaluates all invariants for all vertices of both graphs, sorts them, and lexicographically compares. The upper exclusive bound on the invariant values is then the maximum of the back of both invariant lists plus one. Yes, it is then less clear why invariants must be in a contiguous range with a reasonable upper bound - which, I assume, is purely for the efficiency and memory-compactness of the invariant multiplicity calculation here:  
  ```c++  
  // isomorphism.hpp, line 156 onwards  
  std::vector<vertex1_t> V_mult;  
  BGL_FORALL_VERTICES_T(v, G1, Graph1)  
    V_mult.push_back(v);  
  {  
    std::vector<size_type> multiplicity(max_invariant, 0);  
    BGL_FORALL_VERTICES_T(v, G1, Graph1)  
      ++multiplicity.at(invariant1(v));  
    sort(V_mult, compare_multiplicity(invariant1, &multiplicity[0]));  
  }  
  ```  
  But I think that can be fixed with documentation, too. In principle, we could ignore the named parameter, even if supplied, and remove the ~~undocumented~~ requirement on `vertex_invariant2`, without breaking existing code.  
- We could even go further and remove the range requirement on vertex invariants entirely. The multiplicity of invariants can be calculated efficiently by counting in the sorted list of invariants for one graph from the previous step. If the range requirement is removed, then it will need to be replaced by an `unordered_map` to avoid wasting memory.  
- Something small: The first step of `test_isomorphism`, where all invariants are calculated for all vertices, does not `reserve`.   
  
Thoughts?

---

## Comment 1

> Username: jan-grimo  
> Created at: 2020-02-15 09:47:15 UTC  
> Url: https://github.com/boostorg/graph/issues/203#issuecomment-586573265  

I've implemented all of the ideas in the issue here.  
  
I couldn't find a contributing guide, so I took my cue on comment density from the header itself.  
  
As a guide to a reviewer: The disconnected vertex mapping at the end cannot fail purely because the full sorted lists of invariants of both graphs are compared right at the start before starting the edge matching procedure. Since the matching obeys invariants, it is guaranteed that for each unmatched vertex in `G1`, there is another with a matching invariant in `G2` at `return_point_true`.  
  
I think the vertex matching adds value in particular because since `in_S` is never revealed to consumers, mapping unmatched disconnected vertices outside of the library algorithm is more expensive.  
  
I have no qualms with any part of the PR getting rejected. I'll be happy if the documentation changes are accepted.

---

## Comment 2

> Username: jan-grimo  
> Created at: 2021-06-14 09:18:05 UTC  
> Url: https://github.com/boostorg/graph/issues/203#issuecomment-860533674  

@jzmaddock Could you please assign the referenced MRs to a maintainer or have a look at them yourself? I saw you're the one doing merges recently and that the official maintainer per boost's maintainer list is inactive. @joemalle got bit by disconnected vertices not being mapped by `isomorphism` too, so I think it's worth taking a look.
