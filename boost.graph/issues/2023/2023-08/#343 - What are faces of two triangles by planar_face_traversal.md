# #343 - What are faces of two triangles by planar_face_traversal? [Open]

> Username: fililili  
> Created at: 2023-08-02 08:17:00 UTC  
> Updated at: 2023-08-04 01:36:13 UTC  
> Url: https://github.com/boostorg/graph/issues/343  

Suppose we have two triangles, one have vertexes 0, 1, 2, the other have vertexes 3, 4, 5, then   we use planar_face_traversal to traversal it, we will get 4 faces:  
New face: 0 1 2   
New face: 1 0 2   
New face: 3 4 5   
New face: 4 3 5   
But I think we should only have three faces, we only have three regions in fact.  
Anybody can explain the result?  
Here is the code (https://godbolt.org/z/eKKffh1Mx):  
```cpp  
#include <iostream>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/properties.hpp>  
#include <boost/graph/graph_traits.hpp>  
#include <boost/property_map/property_map.hpp>  
#include <boost/ref.hpp>  
#include <vector>  
  
#include <boost/graph/planar_face_traversal.hpp>  
#include <boost/graph/boyer_myrvold_planar_test.hpp>  
  
using namespace boost;  
  
// Some planar face traversal visitors that will  
// print the vertices and edges on the faces  
  
struct output_visitor : public planar_face_traversal_visitor  
{  
    void begin_face() { std::cout << "New face: "; }  
    void end_face() { std::cout << std::endl; }  
};  
  
struct vertex_output_visitor : public output_visitor  
{  
    template < typename Vertex > void next_vertex(Vertex v)  
    {  
        std::cout << v << " ";  
    }  
};  
  
struct edge_output_visitor : public output_visitor  
{  
    template < typename Edge > void next_edge(Edge e) { std::cout << e << " "; }  
};  
  
int main(int argc, char** argv)  
{  
  
    typedef adjacency_list< vecS, vecS, undirectedS,  
        property< vertex_index_t, int >, property< edge_index_t, int > >  
        graph;  
  
    // Create a graph - this is a biconnected, 3 x 3 grid.  
    // It should have four small (four vertex/four edge) faces and  
    // one large face that contains all but the interior vertex  
    graph g(6);  
  
    add_edge(0, 1, g);  
    add_edge(1, 2, g);  
    add_edge(2, 0, g);  
  
    add_edge(3, 4, g);  
    add_edge(4, 5, g);  
    add_edge(5, 3, g);  
  
    // Initialize the interior edge index  
    property_map< graph, edge_index_t >::type e_index = get(edge_index, g);  
    graph_traits< graph >::edges_size_type edge_count = 0;  
    graph_traits< graph >::edge_iterator ei, ei_end;  
    for (auto [ei, ei_end] = edges(g); ei != ei_end; ++ei)  
        put(e_index, *ei, edge_count++);  
  
    // Test for planarity - we know it is planar, we just want to  
    // compute the planar embedding as a side-effect  
    typedef std::vector< graph_traits< graph >::edge_descriptor > vec_t;  
    std::vector< vec_t > embedding(num_vertices(g));  
    if (boyer_myrvold_planarity_test(boyer_myrvold_params::graph = g,  
            boyer_myrvold_params::embedding = &embedding[0]))  
        std::cout << "Input graph is planar" << std::endl;  
    else  
        std::cout << "Input graph is not planar" << std::endl;  
  
    std::cout << std::endl << "Vertices on the faces: " << std::endl;  
    vertex_output_visitor v_vis;  
    planar_face_traversal(g, &embedding[0], v_vis);  
  
    std::cout << std::endl << "Edges on the faces: " << std::endl;  
    edge_output_visitor e_vis;  
    planar_face_traversal(g, &embedding[0], e_vis);  
  
    return 0;  
}  
```

---

## Comment 1

> Username: fililili  
> Created at: 2023-08-02 10:01:10 UTC  
> Updated at: 2023-08-04 01:36:13 UTC  
> Url: https://github.com/boostorg/graph/issues/343#issuecomment-1661918898  

If I add one more edge 0 to 3 ( `add_edge(3, 0, g);`  
 ), we only have three faces. Add one edge will reduce the number of faces.   
Here is the code (https://godbolt.org/z/f8dGbeY43)  
I think the algorithm can only handle connective graph.
