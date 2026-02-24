# #212 - Function mcgregor_common_subgraphs cannot correctly find subgraph [Open]

> Username: proudhuma  
> Created at: 2020-03-09 12:31:49 UTC  
> Updated at: 2023-07-07 00:27:08 UTC  
> Url: https://github.com/boostorg/graph/issues/212  

Here is my code. My code is trying to find common subgraph between graph1 and graph2 with specific number of vertices.  
  
```  
#include <iostream>  
#include <unordered_map>  
#include <string>  
#include <boost/algorithm/string.hpp>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/vf2_sub_graph_iso.hpp>  
#include <boost/graph/mcgregor_common_subgraphs.hpp>  
#include <boost/property_map/property_map.hpp>  
  
using EdgeProperty = boost::property<boost::edge_name_t, unsigned int>;  
using VertexProperty = boost::property<boost::vertex_name_t, unsigned int, boost::property<boost::vertex_index_t, int> >;  
using Graph = boost::adjacency_list<boost::vecS, boost::vecS, boost::bidirectionalS, VertexProperty, EdgeProperty>;  
  
template<typename GraphFirst, typename GraphSecond>  
struct generate_subgraph_callback {  
  generate_subgraph_callback(const GraphFirst &graph1, const GraphSecond &graph2,  
                             std::vector <Graph> *result, int size) :  
    m_graph1(graph1), m_graph2(graph2), m_result(result), m_subgraph_size(size) {}  
  template<typename CorrespondenceMapFirstToSecond, typename CorrespondenceMapSecondToFirst>  
  bool operator()(CorrespondenceMapFirstToSecond correspondence_map_1_to_2,  
                  CorrespondenceMapSecondToFirst correspondence_map_2_to_1,  
                  typename boost::graph_traits<GraphFirst>::vertices_size_type subgraph_size) {  
    // only when size equals input  
    if (subgraph_size != m_subgraph_size) {  
      return (true);  
    }  
    Graph subgraph;  
    std::vector<int> vertex_set; // vertex_set contains old graph id  
    std::unordered_map<int, int> map;  
  
    BGL_FORALL_VERTICES_T(vertex1, m_graph1, GraphFirst){  
      // skip unmapped vertices  
      if (boost::get(correspondence_map_1_to_2, vertex1) != boost::graph_traits<GraphSecond>::null_vertex()) {  
        vertex_set.push_back(vertex1);  
      }  
    }  
  
    // reconstruct vertices  
    int index = 0;  
    for (auto it = vertex_set.begin(); it != vertex_set.end(); it++) {  
      boost::add_vertex(VertexProperty(boost::get(boost::vertex_name_t(), m_graph1, *it)), subgraph);  
      std::cout << *it << " " << get(boost::vertex_name_t(), m_graph1, *it) << " " << index << std::endl;  
      map[*it] = index;  
      index++;  
    }  
    // reconstruct edges  
    for (auto it = vertex_set.begin(); it != vertex_set.end(); it++) {  
      int index1 = map[*it];  
      for (auto _it = std::next(it); _it != vertex_set.end(); _it++) {  
        int index2 = map[*_it];  
        // check edge exists  
        if (boost::edge(*it, *_it, m_graph1).second) {  
          boost::add_edge(index1, index2,   
            boost::get(boost::edge_name_t(), m_graph1, boost::edge(*it, *_it, m_graph1).first), subgraph);  
        } else if(boost::edge(*_it, *it, m_graph1).second) {  
          boost::add_edge(index2, index1,   
            boost::get(boost::edge_name_t(), m_graph1, boost::edge(*_it, *it, m_graph1).first), subgraph);  
        }  
      }  
    }  
  
    (*m_result).push_back(subgraph);  
    return (true);  
  }  
  
private:  
  const GraphFirst &m_graph1;  
  const GraphSecond &m_graph2;  
  std::vector <Graph> *m_result;  
  int m_subgraph_size;  
};  
  
std::vector <Graph> find_maximal_common_subgraphs_n_vertices(const Graph& g1, const Graph& g2, int n) {  
  // use boost::mcgregor_common_subgraphs  
  auto vertex_comp = boost::make_property_map_equivalent(boost::get(boost::vertex_name, g1), boost::get(boost::vertex_name, g2));  
  auto edge_comp = boost::make_property_map_equivalent(boost::get(boost::edge_name, g1), boost::get(boost::edge_name, g2));  
  
  std::vector <Graph> result;  
  // store each common subgraph into result  
  generate_subgraph_callback<Graph, Graph> callback(g1, g2, &result, n);  
  boost::mcgregor_common_subgraphs_maximum_unique(g1, g2, true, callback, boost::edges_equivalent(edge_comp).vertices_equivalent(vertex_comp));  
  
  return result;  
}  
  
int main(){  
  Graph graph1;  
  boost::add_vertex(VertexProperty(11), graph1);  
  boost::add_vertex(VertexProperty(12), graph1);  
  boost::add_vertex(VertexProperty(13), graph1);  
  boost::add_vertex(VertexProperty(10), graph1);  
  boost::add_edge(0, 1, EdgeProperty(1), graph1);  
  boost::add_edge(1, 2, EdgeProperty(1), graph1);  
  boost::add_edge(2, 3, EdgeProperty(1), graph1);  
  boost::add_edge(3, 1, EdgeProperty(1), graph1);  
   
  Graph graph2;  
  boost::add_vertex(VertexProperty(11), graph2);  
  boost::add_vertex(VertexProperty(12), graph2);  
  boost::add_vertex(VertexProperty(13), graph2);  
  boost::add_vertex(VertexProperty(10), graph2);  
  boost::add_edge(0, 1, EdgeProperty(1), graph2);  
  boost::add_edge(1, 2, EdgeProperty(1), graph2);  
  boost::add_edge(2, 3, EdgeProperty(1), graph2);  
  boost::add_edge(3, 0, EdgeProperty(1), graph2);  
  
  std::vector <Graph> result = find_maximal_common_subgraphs_n_vertices(graph1, graph2, 4);  
  std::cout << result.size() << std::endl;  
  
  return 0;  
}  
```  
  
It is clear that 11->12->13->10 should be a common subgraph with 4 vertices while result size is 0. I looked at source code of function mcgregor_common_subgraphs and I believe the reason is in below code segment.  
  
```  
if (!is_undirected2)  
                {  
  
                    // Search for edge from new to existing vertex (graph2)  
                    BGL_FORALL_OUTEDGES_T(  
                        new_vertex2, edge2, graph2, GraphSecond)  
                    {  
                        if (target(edge2, graph2) == existing_vertex2)  
                        {  
                            edge_from_new2 = edge2;  
                            edge_from_new_exists2 = true;  
                            break;  
                        }  
                    }  
                }  
  
                // Make sure edges from new to existing vertices are equivalent  
                if ((edge_from_new_exists1 != edge_from_new_exists2)  
                    || ((edge_from_new_exists1 && edge_from_new_exists2)  
                        && !edges_equivalent(edge_from_new1, edge_from_new2)))  
                {  
  
                    return (false);  
                }  
  
                if ((edge_from_new_exists1 && edge_from_new_exists2)  
                    || (edge_to_new_exists1 && edge_to_new_exists2))  
                {  
                    has_one_edge = true;  
                }  
```  
  
Here when subgraph is a graph containing nodes 11,12,13 and we want to extend node 10, mcgregor_common_subgraphs finds out there is an edge between 10 and 11 in subgraph2 while ther e is no edge between 10 and 11 in subgraph1. Hence can_extend_graph will return false. I believe the implementation of mcgregor_common_subgraphs now can only find subgraphs that appears "fully" in both graphs, which will miss many common subgraphs.

---

## Comment 1

> Username: proudhuma  
> Created at: 2020-03-10 03:57:15 UTC  
> Url: https://github.com/boostorg/graph/issues/212#issuecomment-596889735  

Also, it is not correct to make sure ALL edges from existing to new vertices are equivalent. At least one edge from existing to new vertices is equivalent is enough.

---

## Comment 2

> Username: nescio007  
> Created at: 2020-07-02 17:43:24 UTC  
> Url: https://github.com/boostorg/graph/issues/212#issuecomment-653139737  

Hi, I noticed the same issue.  
  
For what it's worth, here is the example given in the 1982 McGregor paper ["_Backtrack Search Algorithms and the Maximal Common Subgraph Problem_"](https://onlinelibrary.wiley.com/doi/pdf/10.1002/spe.4380120103)  
  
![mcgregor1](https://user-images.githubusercontent.com/7250294/86391387-07332800-bc9a-11ea-81f0-0e3a3a409040.png)  
  
![mcgregor2](https://user-images.githubusercontent.com/7250294/86391498-3184e580-bc9a-11ea-8708-7ae3a334c9c2.png)  
  
I.e., the correspondence here should be  
```  
1 <-> 1  
2 <-> 2  
3 <-> 3  
4 <-> 4  
5 <-> 5  
6 <-> 7 (*)  
7 <-> 8  
8 <-> 6  
```  
  
(*) It appears that `6 <-> 7` should be included, even though they share no edges. This also makes sense, as the paper states that every node of the smaller graph should be included in the correspondence  
  
> G_1 has p_1 nodes [...] G_2 has p_2 nodes [...]. We shall assume [...] that p_1 <= p_2 **and that every node in G_1 must therefore be included in the correspondence**.  
  
I tried to reproduce this with the boost implementation (note that code uses 0-based indexing, whereas the paper is 1-based):  
  
```c++  
#include <iostream>  
  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/mcgregor_common_subgraphs.hpp>  
  
using namespace boost;  
  
typedef adjacency_list<vecS, vecS, undirectedS> Graph;  
typedef adjacency_list<> DiGraph;  
  
template<typename GraphFirst,  
        typename GraphSecond>  
struct print_callback {  
  
    print_callback(const GraphFirst &graph1,  
                   const GraphSecond &graph2) :  
            m_graph1(graph1), m_graph2(graph2) {}  
  
    template<typename CorrespondenceMapFirstToSecond,  
            typename CorrespondenceMapSecondToFirst>  
    bool operator()(CorrespondenceMapFirstToSecond correspondence_map_1_to_2,  
                    CorrespondenceMapSecondToFirst correspondence_map_2_to_1,  
                    typename graph_traits<GraphFirst>::vertices_size_type subgraph_size) {  
  
        // Print out correspondences between vertices  
        BGL_FORALL_VERTICES_T(vertex1, m_graph1, GraphFirst) {  
  
                // Skip unmapped vertices  
                if (get(correspondence_map_1_to_2, vertex1) != graph_traits<GraphSecond>::null_vertex()) {  
                    std::cout << vertex1 << " <-> " << get(correspondence_map_1_to_2, vertex1) << std::endl;  
                }  
  
            }  
  
        std::cout << "---" << std::endl;  
  
        return (true);  
    }  
  
private:  
    const GraphFirst &m_graph1;  
    const GraphSecond &m_graph2;  
  
};  
  
  
int main() {  
  
    std::vector<std::pair<int, int>> g1_edges = {  
            {0, 1}, {0, 2}, {1, 2}, {1, 3}, {1, 4}, {2, 5}, {2, 7}, {3, 6}, {4, 6}, {4, 7}  
    };  
    std::vector<std::pair<int, int>> g2_edges = {  
            {0, 1}, {0, 2}, {1, 3}, {1, 4}, {2, 5}, {3, 6}, {4, 5}, {4, 7}  
    };  
  
    Graph G1(g1_edges.begin(), g1_edges.end(), 8);  
    Graph G2(g2_edges.begin(), g2_edges.end(), 8);  
  
    std::cout << "Graph G1:" << std::endl;  
    for (auto v : G1.vertex_set()) {  
        std::cout << "\t" << v << ": ";  
        for (auto e: G1.out_edge_list(v)) {  
            std::cout << e.get_target() << ", ";  
        }  
        std::cout << std::endl;  
    }  
  
    std::cout << "Graph G2:" << std::endl;  
    for (auto v : G2.vertex_set()) {  
        std::cout << "\t" << v << ": ";  
        for (auto e: G2.out_edge_list(v)) {  
            std::cout << e.get_target() << ", ";  
        }  
        std::cout << std::endl;  
    }  
  
    print_callback<Graph, Graph> my_callback(G1, G2);  
    mcgregor_common_subgraphs_maximum_unique(G1, G2, false, my_callback);  
  
    return 0;  
}  
```

---

## Comment 3

> Username: chakpongchung  
> Created at: 2022-10-24 20:08:42 UTC  
> Url: https://github.com/boostorg/graph/issues/212#issuecomment-1289544299  

what is your cmake setting and your boost version? Are you using boost managed by conda environment?

---

## Comment 4

> Username: ishandutta2007  
> Created at: 2022-11-04 14:31:06 UTC  
> Updated at: 2022-11-05 06:00:19 UTC  
> Url: https://github.com/boostorg/graph/issues/212#issuecomment-1303647964  

Yes vf2 seems to be wrong,  
Here is my [code](https://ideone.com/BS1FSP)  
Clearly there is atleast one subgraph isomer in my data.  
But it finds none  
  
I am not even using mcgregor. I a just running your subgraph isomer  [example ](https://github.com/boostorg/graph/blob/develop/example/vf2_sub_graph_iso_example.cpp)with a different data  
  
Ideone's Boost Version is `1.65.1`

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2023-07-07 00:27:08 UTC  
> Url: https://github.com/boostorg/graph/issues/212#issuecomment-1624469148  

If you don't know the solution to the problem, you can still help greatly by creating a pull request with a good unit test that demonstrates the problem. Sometimes, that's half-way to fixing it.
