# #273 - Bug report on the function boyer_myrvold_planarity_test. [Closed]

> Username: matheusdiogenesandrade  
> Created at: 2021-08-31 02:42:53 UTC  
> Updated at: 2021-08-31 18:43:22 UTC  
> Closed at: 2021-08-31 18:42:48 UTC  
> Url: https://github.com/boostorg/graph/issues/273  

Greetings.  
  
My name is Matheus Diógenes Andrade, and I would like to report a bug in the function **boyer_myrvold_planarity_test** from the **Boost Graph Library**. The instance graph is given in the [picture](https://drive.google.com/file/d/1q0GesdKrVuyACnuuVBPBph1nHCAgVBbU/view?usp=sharing).  The executed code is given below.  
  
```cpp  
#include <iostream>  
#include <vector>  
  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/properties.hpp>  
#include <boost/graph/graph_traits.hpp>  
#include <boost/property_map/property_map.hpp>  
#include <boost/graph/planar_face_traversal.hpp>  
#include <boost/graph/boyer_myrvold_planar_test.hpp>  
#include <boost/ref.hpp>  
  
using namespace boost;  
using namespace std;  
  
struct output_visitor: public planar_face_traversal_visitor {  
  template <typename Vertex> void next_vertex(Vertex v) {   
    cout << v << " ";   
  }   
  void end_face() {   
    cout << endl;   
  }  
};  
  
int main() {  
  // building graph  
  typedef adjacency_list<vecS, vecS, undirectedS, property<vertex_index_t, int>, property<edge_index_t, int>> graph;  
  graph g(21);  
  // add edges  
  add_edge(1, 2, g);  
  add_edge(1, 5, g);  
  add_edge(1, 7, g);  
  add_edge(2, 3, g);  
  add_edge(2, 4, g);  
  add_edge(3, 4, g);  
  add_edge(3, 6, g);  
  add_edge(3, 8, g);  
  add_edge(4, 5, g);  
  add_edge(4, 6, g);  
  add_edge(7, 8, g);  
  add_edge(7, 9, g);  
  add_edge(8, 10, g);  
  add_edge(9, 10, g);  
  add_edge(9, 11, g);  
  add_edge(9, 14, g);  
  add_edge(10, 20, g);  
  add_edge(11, 12, g);  
  add_edge(12, 13, g);  
  add_edge(13, 14, g);  
  add_edge(13, 16, g);  
  add_edge(14, 15, g);  
  add_edge(15, 17, g);  
  add_edge(15, 18, g);  
  add_edge(16, 17, g);  
  add_edge(17, 19, g);  
  add_edge(18, 19, g);  
  add_edge(18, 20, g);  
  add_edge(19, 21, g);  
  add_edge(20, 21, g);  
  // inserting edges in the map  
  property_map< graph, edge_index_t >::type e_index = get(edge_index, g);  
  graph_traits< graph >::edges_size_type edge_count = 0;  
  graph_traits< graph >::edge_iterator ei,   
                                       ei_end;  
  cout << "Edges:" << endl;  
  for (tie(ei, ei_end) = edges(g); ei != ei_end; ++ei) {  
    cout << "\t" << *ei << endl;  
    put(e_index, *ei, edge_count++);  
  }  
  cout << edge_count << " edges inserted" << endl;  
  // test for planarity and compute the planar embedding  
  typedef vector< graph_traits< graph >::edge_descriptor > vec_t;  
  vector< vec_t > embedding(num_vertices(g));  
  boyer_myrvold_planarity_test(boyer_myrvold_params::graph = g, boyer_myrvold_params::embedding = &embedding[0]);  
  // print faces  
  cout << "Faces:" << endl;  
  output_visitor my_visitor;  
  planar_face_traversal(g, &embedding[0], my_visitor);  
  return 0;  
}  
```  
  
After executing the algorithm the following text is printed.  
  
```  
Edges:  
        (1,2)  
        (1,5)  
        (1,7)  
        (2,3)  
        (2,4)  
        (3,4)  
        (3,6)  
        (3,8)  
        (4,5)  
        (4,6)  
        (7,8)  
        (7,9)  
        (8,10)  
        (9,10)  
        (9,11)  
        (9,14)  
        (10,20)  
        (11,12)  
        (12,13)  
        (13,14)  
        (13,16)  
        (14,15)  
        (15,17)  
        (15,18)  
        (16,17)  
        (17,19)  
        (18,19)  
        (18,20)  
        (19,21)  
        (20,21)  
30 edges inserted  
Faces:  
1 2 3 8 7  
2 1 5 4  
5 1 7 9 10 8 3 4  
3 2 4 6  
4 3 6  
7 8 10 20 18 15 14 9  
10 9 11 12 13 16 17 19 21 20  
11 9 14 13 12  
13 14 15 17 16  
17 15 18 19  
19 18 20 21  
```  
  
If you compare the returned faces with the actual faces of the graph, you can see that although the algorithm returns some valid faces, such as:  
  
- 1 2 3 8 7;  
- 2 1 5 4;  
- 4 3 6;  
- 11 9 14 13 12;  
- 13 14 15 17 16;  
- 17 15 18 19;  
- 19 18 20 21;  
  
there is a set of faces not being reported by the algorithm, such as:  
  
- 7 9 10 8  
- 9 14 15 18 20 10  
  
One interesting thing is that these two nonreported faces are reported *partially* by the faces (although I do not see them as faces, not even outer faces, just as cycles):  
  
- 5 1 **7 9 10 8** 3 4  
- **7 8 10** 20 18 15 14 **9**  
- **10 9** 11 12 13 16 17 19 21 **20**  
  
I would like to know if anyone has ever faced this sort of bug. Or if it is just a code's error.  
  
Very thank you for your time, and best regards.

---

## Comment 1

> Username: matheusdiogenesandrade  
> Created at: 2021-08-31 18:42:45 UTC  
> Updated at: 2021-08-31 18:43:22 UTC  
> Url: https://github.com/boostorg/graph/issues/273#issuecomment-909503431  

Sorry. It was my mistake. I noticed that the planar embedding may not be the same as the [picture](https://drive.google.com/file/d/1q0GesdKrVuyACnuuVBPBph1nHCAgVBbU/view?usp=sharing), and, hence, we may have different faces as results. The [picture](https://drive.google.com/file/d/1biU-yDEwyBcHXF_uZiQFddk48rpo2eU-/view?usp=sharing) is a planar embedding that contains all the returned faces.
