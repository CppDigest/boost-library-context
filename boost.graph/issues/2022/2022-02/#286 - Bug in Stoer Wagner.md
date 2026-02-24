# #286 - Bug in Stoer Wagner [Closed]

> Username: etiennedeg  
> Created at: 2022-02-16 08:51:22 UTC  
> Updated at: 2022-07-12 06:03:59 UTC  
> Closed at: 2022-07-12 06:02:27 UTC  
> Url: https://github.com/boostorg/graph/issues/286  

**MWE:**  
  
``` c++  
#include <iostream>                  // for std::cout  
#include <utility>                   // for std::pair  
#include <boost/graph/graph_traits.hpp>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/one_bit_color_map.hpp>  
#include <boost/graph/stoer_wagner_min_cut.hpp>  
#include <boost/property_map/property_map.hpp>  
  
using namespace boost;  
  
int main(int,char*[])  
{  
    typedef adjacency_list < listS, vecS, undirectedS,  
    no_property, property < edge_weight_t, int > > Graph;  
    typedef std::pair<int, int> Edge;  
  
    const int num_nodes = 8;  
    Edge edge_array[] =  
    {Edge(0,1), Edge(0,2), Edge(0,3),  
    Edge(1,2), Edge(1,3), Edge(2,3),  
    Edge(4,5), Edge(4,6), Edge(4,7),  
    Edge(5,6), Edge(5,7), Edge(6,7),  
    Edge(0,4)  
    };  
    int weights[] = {3,3,3,2,2,2,3,3,3,2,2,2,6};  
    const int num_edges = sizeof(edge_array)/sizeof(edge_array[0]);  
  
    // declare a graph object  
    Graph g(edge_array, edge_array + num_edges, weights, num_nodes);  
    property_map<Graph, edge_weight_t>::type weightmap = get(edge_weight, g);  
  
    // define a property map, `parities`, that will store a boolean value for each vertex.  
    // Vertices that have the same parity after `stoer_wagner_min_cut` runs are on the same side of the min-cut.  
    BOOST_AUTO(parities, make_one_bit_color_map(num_vertices(g), get(vertex_index, g)));  
    int w = boost::stoer_wagner_min_cut(g, get(edge_weight, g), parity_map(parities));  
  
    std::cout << "The min-cut weight of G is " << w << ".\n" << std::endl;  
  
    std::cout << "One set of vertices consists of:" << std::endl;  
    size_t i;  
    for (i = 0; i < num_vertices(g); ++i) {  
        if (get(parities, i))  
            std::cout << i << std::endl;  
        }  
    std::cout << std::endl;  
  
    std::cout << "The other set of vertices consists of:" << std::endl;  
    for (i = 0; i < num_vertices(g); ++i) {  
        if (!get(parities, i))  
            std::cout << i << std::endl;  
    }  
    std::cout << std::endl;  
    return 0;  
}  
```  
  
**Output:**  
```  
The min-cut weight of G is 7.  
  
One set of vertices consists of:  
0  
1  
2  
3  
4  
5  
7  
  
The other set of vertices consists of:  
6  
```  
  
**Expected Output:**  
```  
The min-cut weight of G is 6.  
  
One set of vertices consists of:  
0  
1  
2  
3  
  
The other set of vertices consists of:  
4  
5  
6  
7  
```  
**Cause:**  
The actual code performs one mincut phase from each vertex of the graph, without ever merging two vertices. In a correct implementation of Stoer-Wagner, we should merge the two last vertices encountered at the end of each mincut phase. Starting vertex of the mincut phase as not importance.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-02-19 19:57:24 UTC  
> Url: https://github.com/boostorg/graph/issues/286#issuecomment-1046092489  

Thank you for finding and documenting this.  
Could you open a pull request that adds this case as a unit test? Are you able to fix the implementation too?

---

## Comment 2

> Username: etiennedeg  
> Created at: 2022-06-03 09:53:21 UTC  
> Url: https://github.com/boostorg/graph/issues/286#issuecomment-1145797476  

Sorry for the delay, I was a bit busy these days, I made a PR for a non-regression test. I think we can probably revert the commit https://github.com/boostorg/graph/commit/393c072c186f6711d1537225dc116ea1528140ac, it seems to be the one introducing the bug. As it is very old, there are probably some more changes to make the old version work.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2022-06-08 01:05:18 UTC  
> Url: https://github.com/boostorg/graph/issues/286#issuecomment-1149332451  

I don't think we can simply revert that commit, since it extracted `maximum_adjacency_search` as a distinct algorithm.   
So, have you tested the algorithm before that commit and it works correctly? You could do that by retargeting your unit test to just before that commit was introduced (as opposed to trying to revert it).  
  
That commit appears to be doing a (large) refactor, so if it did introduce the bug, I hope it was a matter of accidentally deleting the vertex merging logic that you mentioned, and fixing it is simply a matter of putting it back. "SImply" :)

---

## Comment 4

> Username: etiennedeg  
> Created at: 2022-06-08 21:01:37 UTC  
> Url: https://github.com/boostorg/graph/issues/286#issuecomment-1150411106  

Did not test but the old algorithm seems to do the merging of vertices, so there are chances that it is correct. The new commit removed the vertex merging logic, but this is the trickiest part to implement the algorithm. I don't have that much bandwidth these days, I hope I can take a closer look not too far away in the future.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2022-06-09 05:41:19 UTC  
> Url: https://github.com/boostorg/graph/issues/286#issuecomment-1150693354  

Right, so there is a bit of a challenge ahead then. I also have almost no free time at the moment, but if I do find some then I'll try to have a look and make some notes here.

---

## Comment 6

> Username: sebrockm  
> Created at: 2022-07-11 23:06:50 UTC  
> Url: https://github.com/boostorg/graph/issues/286#issuecomment-1181028373  

@etiennedeg   
I was hunting down a bug in one of my project for weeks. Finally, I stumbled over this issue and it really is the root cause for my problem! Thank you so much for finding this!!!  
  
@jeremy-murphy Based on @etiennedeg's test in #294 I created #296 which passes that test. Unfortunately,  
I failed making it work using `maximum_adjacency_search`. After some trial and error I had to give up on that attempt and decided to do a simple rollback of the `stoer_wagner_min_cut.hpp` changes in https://github.com/boostorg/graph/commit/393c072c186f6711d1537225dc116ea1528140ac.  
I'm afraid the refactoring done there went wrong in more than one way :(   
I collected my findings in #297 which, ideally, would be fixed together with this one, but for now I cannot spend any more time on that. So, hopefully the fix in #294 is fine for now.

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2022-07-12 06:03:59 UTC  
> Url: https://github.com/boostorg/graph/issues/286#issuecomment-1181351241  

Again, thanks to both of you for your contributions to getting this fixed.
