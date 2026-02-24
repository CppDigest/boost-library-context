# #389 - Bug in `undirected_dfs` causing incorrect callbacks to `finish_edge` of `dfs_visitor` [Closed]

> Username: danielyxyang  
> Created at: 2024-10-08 14:20:26 UTC  
> Updated at: 2024-10-30 06:47:55 UTC  
> Closed at: 2024-10-30 06:47:55 UTC  
> Url: https://github.com/boostorg/graph/issues/389  

## Minimal working example  
<details>  
<summary>Code</summary>  
  
```c++  
#include <iostream>  
  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/undirected_dfs.hpp>  
  
using Graph = boost::adjacency_list<  
    boost::vecS,  
    boost::vecS,  
    boost::undirectedS,  
    boost::no_property,  
    boost::property<boost::edge_color_t, boost::default_color_type>>;  
  
using Vertex = boost::graph_traits<Graph>::vertex_descriptor;  
using Edge = boost::graph_traits<Graph>::edge_descriptor;  
  
struct DFSVisitorLogger : boost::default_dfs_visitor {  
  void log_vertex(const Vertex v, const std::string &event) {  
    std::cout << "vertex " << v << " " << event << "\n";  
  }  
  void log_edge(const Edge e, const std::string &event, const Graph &g) {  
    std::cout << "edge (" << boost::source(e, g) << "," << boost::target(e, g) << ") " << event << "\n";  
  }  
  
  void discover_vertex(Vertex v, const Graph &g) {  
    log_vertex(v, "discovered");  
  }  
  void finish_vertex(Vertex v, const Graph &g) {  
    log_vertex(v, "finished");  
  }  
  void examine_edge(Edge e, const Graph &g) {  
    log_edge(e, "examined", g);  
  }  
  void tree_edge(Edge e, const Graph &g) {  
    log_edge(e, "tree", g);  
  }  
  void back_edge(Edge e, const Graph &g) {  
    log_edge(e, "back", g);  
  }  
  void forward_or_cross_edge(Edge e, const Graph &g) {  
    log_edge(e, "forward_cross", g);  
  }  
  void finish_edge(Edge e, const Graph &g) {  
    log_edge(e, "finished", g);  
  }  
};  
  
int main() {  
  Graph g(3);  
  boost::add_edge(0, 1, g);  
  boost::add_edge(1, 2, g);  
  
  DFSVisitorLogger dfs_visitor_logger;  
  boost::undirected_dfs(g,  
      boost::visitor(dfs_visitor_logger)  
      .edge_color_map(boost::get(boost::edge_color, g)));  
}  
```  
</details>  
  
Example Graph  
```  
0 (start) -- 1 -- 2  
```  
  
Actual output:  
```  
vertex 0 discovered  
edge (0,1) examined  
edge (0,1) tree  
vertex 1 discovered  
edge (1,0) examined  
edge (1,0) finished  
edge (1,2) examined  
edge (1,2) tree  
vertex 2 discovered  
edge (2,1) examined  
edge (2,1) finished  
vertex 2 finished  
edge (1,2) finished  
vertex 1 finished  
edge (1,2) finished   <-- should be "edge (0, 1) finished"  
vertex 0 finished  
edge (0,1) finished   <-- should not appear  
```  
  
Expected output:  
```  
vertex 0 discovered  
edge (0,1) examined  
edge (0,1) tree  
vertex 1 discovered  
edge (1,0) examined  
edge (1,0) finished  
edge (1,2) examined  
edge (1,2) tree  
vertex 2 discovered  
edge (2,1) examined  
edge (2,1) finished  
vertex 2 finished  
edge (1,2) finished  
vertex 1 finished  
edge (0,1) finished  
vertex 0 finished  
```  
  
## Problem  
The problem is that in [`undirected_dfs.hpp:61`](https://github.com/boostorg/graph/blob/ba3022a97d6ee0c7520425b3fa5a45404efb7908/include/boost/graph/undirected_dfs.hpp#L61) the following invariant for the entries `VertexInfo& back = (u, (src_e, out_e))` in the `stack` is established:  
- `u`: vertex  
- `src_e`: edge used to discover vertex `u` (i.e. `u` is the **target** of `src_e`)  
- `out_e`: set of edges adjacent to `u` not yet examined  
```  
                 --out_e-->  
                /  
--src_e--> (u)  ---out_e-->  
                \  
                 --out_e-->  
```  
However, [`undirected_dfs.hpp:82`](https://github.com/boostorg/graph/blob/ba3022a97d6ee0c7520425b3fa5a45404efb7908/include/boost/graph/undirected_dfs.hpp#L82) violates this invariant by pointing `src_e` to the next edge `*ei = (u, v)` in the previous line 81 (i.e. `u` is the **source** vertex) and then pushes `(u, (src_e, out_e))` onto the stack, which corresponds to:   
```  
                 --src_e-->  
                /  
--  .  --> (u)  ---out_e-->  
                \  
                 --out_e-->  
```  
  
## Solution  
See pull request #390  
  
## Related issues  
Issue related to #222 and pull request #129, but the solution proposed there does not properly establish the described invariant.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2024-10-09 03:39:05 UTC  
> Url: https://github.com/boostorg/graph/issues/389#issuecomment-2401204777  

Thanks for the detailed explanation, proposed solution and references to existing issues/PRs. An exemplary issue!  
  
Now if you can just codify your example here (without all the strings) as a unit test, the proposed solution will be perfect.  :)  
  
Thanks!

---

## Comment 2

> Username: danielyxyang  
> Created at: 2024-10-24 08:34:33 UTC  
> Url: https://github.com/boostorg/graph/issues/389#issuecomment-2434640502  

PR has been updated with a unit test. Let me know if anything else is missing :)
