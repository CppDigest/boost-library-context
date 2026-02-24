# #284 - Visitor is passed by value in `breadth_first_search()` [Closed]

> Username: mestag-a  
> Created at: 2022-01-27 17:36:10 UTC  
> Updated at: 2022-02-17 22:41:11 UTC  
> Closed at: 2022-02-17 14:58:17 UTC  
> Url: https://github.com/boostorg/graph/issues/284  

I stumbled accross what I tought to be an unintuitive behavior when using `breadth_first_search()`, it seems the visitor is passed by value at some point.  
  
So, the visitor mutates through the algorithm, but when you get out of `breadth_first_search()`, you get the original back.  
For example ([Compiler Explorer link](https://godbolt.org/z/nqP4YfhPo)) :  
```C++  
#include <iostream>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/breadth_first_search.hpp>  
  
  
using Graph = boost::adjacency_list<  
        boost::setS, boost::vecS, boost::directedS  
>;  
enum Node { A, B, C, D, E };  
  
  
struct Visitor : public boost::default_bfs_visitor  
{  
    void finish_vertex(Graph::vertex_descriptor u, Graph const& g) {  
        i++;  
  
        std::cout << "Finished node n°" << i << '\n';  
    }  
  
    int i = 0;  
};  
  
  
int main() {  
    Graph g;  
  
    boost::add_edge(A, B, g);  
    boost::add_edge(B, C, g);  
    boost::add_edge(C, D, g);  
    boost::add_edge(D, E, g);  
  
    Visitor v{};  
    boost::breadth_first_search(g, A, boost::visitor(v));  
  
    std::cout << "Found total of " << v.i << " nodes\n";  
    return 0;  
}  
```  
  
Which outputs :  
```  
Finished node n°1  
Finished node n°2  
Finished node n°3  
Finished node n°4  
Finished node n°5  
Found total of 0 nodes  
```  
  
Isn't this weird ? Am I wrong to assume this is unintuitive or did I miss something ?

---

## Comment 1

> Username: mestag-a  
> Created at: 2022-02-17 14:58:17 UTC  
> Url: https://github.com/boostorg/graph/issues/284#issuecomment-1043039408  

So it seems I did miss something, as it's clearly written in [the documentation](https://www.boost.org/doc/libs/1_78_0/libs/graph/doc/breadth_first_search.html) that the visitor is passed by value.  
> A visitor object that is invoked inside the algorithm at the event-points specified by the BFS Visitor concept. The visitor object is passed by value [[1]](https://www.boost.org/doc/libs/1_78_0/libs/graph/doc/breadth_first_search.html#1).  
  
The footnote is even clearer  
> [[1]](https://www.boost.org/doc/libs/1_78_0/libs/graph/doc/breadth_first_search.html#1) Since the visitor parameter is passed by value, if your visitor contains state then any changes to the state during the algorithm will be made to a copy of the visitor object, not the visitor object passed in. Therefore you may want the visitor to hold this state by pointer or reference.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2022-02-17 22:40:26 UTC  
> Updated at: 2022-02-17 22:41:11 UTC  
> Url: https://github.com/boostorg/graph/issues/284#issuecomment-1043557260  

Interesting. This actually seems like a mistake in the original design. Using the visitor to hold state by pointer/reference is just icky. BGL follows the philosophy of the STL pretty closely, in which case the visitor should be returned (by value), as it is in `std::for_each`.  
Since both `breadth_first_search` and `depth_first_search` currently return `void`... I think there is room for improvement.  
Thanks for bringing this to my attention!
