# #365 - Option to allow `breadth_first_search` to use `in_edges` [Open]

> Username: byjtew  
> Created at: 2024-02-21 13:29:57 UTC  
> Updated at: 2024-02-21 13:29:57 UTC  
> Url: https://github.com/boostorg/graph/issues/365  

**Context:** You want to create a `predecessors(source_node)` function gathering the vertices that point to a given `source_vertex`, you'd want to use a BFS visitor, which is currently impossible since the usage of the `out_edges` iterator is hardcoded in its behaviour.  
  
**Feature request:** A new function `inv_breadth_first_search`, or an extra template parameter to specify which iterator to use (`in_edges`/`out_edges`)  
  
  
Here's the local version that we use:  
```c++  
template<class IncidenceGraph, class Buffer, class BFSVisitor, class ColorMap, class SourceIterator>  
void inv_breadth_first_visit(const IncidenceGraph &g, SourceIterator sources_begin, SourceIterator sources_end,  
                             Buffer &Q, BFSVisitor vis, ColorMap color) {  
    BOOST_CONCEPT_ASSERT((IncidenceGraphConcept<IncidenceGraph>));  
    typedef graph_traits<IncidenceGraph> GTraits;  
    typedef typename graph_traits<IncidenceGraph>::vertex_descriptor Vertex;  
    BOOST_CONCEPT_ASSERT((BFSVisitorConcept<BFSVisitor, IncidenceGraph>));  
    BOOST_CONCEPT_ASSERT((ReadWritePropertyMapConcept<ColorMap, Vertex>));  
    typedef typename property_traits<ColorMap>::value_type ColorValue;  
    typedef color_traits<ColorValue> Color;  
    typename GTraits::in_edge_iterator ei, ei_end;  
  
    for (; sources_begin != sources_end; ++sources_begin) {  
        Vertex s = *sources_begin;  
        put(color, s, Color::gray());  
        vis.discover_vertex(s, g);  
        Q.push(s);  
    }  
    while (!Q.empty()) {  
        Vertex u = Q.top();  
        Q.pop();  
        vis.examine_vertex(u, g);  
        for (boost::tie(ei, ei_end) = in_edges(u, g); ei != ei_end; ++ei) {  
            Vertex v = target(*ei, g);  
            vis.examine_edge(*ei, g);  
            ColorValue v_color = get(color, v);  
            if (v_color == Color::white()) {  
                vis.tree_edge(*ei, g);  
                put(color, v, Color::gray());  
                vis.discover_vertex(v, g);  
                Q.push(v);  
            } else {  
                vis.non_tree_edge(*ei, g);  
                if (v_color == Color::gray())  
                    vis.gray_target(*ei, g);  
                else  
                    vis.black_target(*ei, g);  
            }  
        } // end for  
        put(color, u, Color::black());  
        vis.finish_vertex(u, g);  
    } // end while  
}  
```  
  
cc @raphael-s-steiner
