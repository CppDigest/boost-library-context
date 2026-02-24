# #308 - Can't remove edge after copying a graph [Open]

> Username: TheStoryOfUnlimitedDreams  
> Created at: 2022-10-20 03:22:29 UTC  
> Updated at: 2022-10-20 03:22:29 UTC  
> Url: https://github.com/boostorg/graph/issues/308  

I'm trying to construct a graph with its copy constructor. It runs successfully.  I could access its property with the descriptors of previous graph. So I'm think the descriptors in a grpah are not changed  after copy. The problem is that a crash occurs when I'm trying to remove an edge with edge_descriptor of previous graph.   
I want to know whether the algorithm in copy constructor goes wrong or I use it in wrong way.  
Here's my test code.  
```  
#include <boost/graph/graph_traits.hpp>  
#include <boost/graph/adjacency_list.hpp>  
#include <iostream>                  // for std::cout  
#include <utility>                   // for std::pair  
  
int Indexdemo1(void)  
{  
    using namespace boost;  
    // create a typedef for the Graph type  
    typedef adjacency_list<vecS, vecS, undirectedS> Graph;  
  
    // Make convenient labels for the vertices  
    enum { A, B, C, D, E, N };  
    const int num_vertices = N;  
    const char* name = "ABCDE";  
  
    // writing out the edges in the graph  
    typedef std::pair<int, int> Edge;  
    Edge edge_array[] =  
    { Edge(A,B), Edge(A,D), Edge(C,A), Edge(D,C),  
      Edge(C,E), Edge(B,D), Edge(D,E) };  
    const int num_edges = sizeof(edge_array) / sizeof(edge_array[0]);  
  
    Graph g(edge_array, edge_array + sizeof(edge_array) / sizeof(Edge), num_vertices);  
  
    typedef graph_traits<Graph>::vertex_descriptor Vertex;  
  
    // get the property map for vertex indices  
    typedef property_map<Graph, vertex_index_t>::type IndexMap;  
    IndexMap index = get(vertex_index, g);  
  
    std::cout << "vertices(g) = ";  
    typedef graph_traits<Graph>::vertex_iterator vertex_iter;  
    std::pair<vertex_iter, vertex_iter> vp;  
    for (vp = vertices(g); vp.first != vp.second; ++vp.first) {  
        Vertex v = *vp.first;  
        std::cout << index[v] << " ";  
    }  
    std::cout << std::endl;  
  
    std::cout << "edges(g) = ";  
    graph_traits<Graph>::edge_iterator ei, ei_end;  
    for (boost::tie(ei, ei_end) = edges(g); ei != ei_end; ++ei)  
        std::cout << "(" << index[source(*ei, g)]  
        << "," << index[target(*ei, g)] <<") ";  
    std::cout << std::endl;  
  
    Graph G(g);  
    std::cout << "edges(G) = ";  
// access the G with descriptors of  g.  
    for (boost::tie(ei, ei_end) = edges(g); ei != ei_end; ++ei)  
        std::cout << "(" << index[source(*ei, G)]  
        << "," << index[target(*ei, G)] << ") ";  
    std::cout << std::endl;  
//It runs successfully before.  
    auto e = *edges(g).first;  
    //G.remove_edge(e);//not OK  
    g.remove_edge(e);//OK  
    std::cout << "edge descriptor could be used for access but can't be used fo erase." << std::endl;  
      
    return 0;  
}  
```  
The VS 2022 Runtime Library throw a debug assertion of "list erase iterator outside range" when it runs G.remove_edge(e).
