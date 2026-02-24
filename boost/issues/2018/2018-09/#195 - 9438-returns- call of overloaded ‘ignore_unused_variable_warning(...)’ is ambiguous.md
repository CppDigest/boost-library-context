# #195 - 9438-returns: call of overloaded ‘ignore_unused_variable_warning(...)’ is ambiguous [Closed]

> Username: Magnati  
> Created at: 2018-09-21 09:09:57 UTC  
> Updated at: 2018-09-21 13:11:17 UTC  
> Closed at: 2018-09-21 13:11:17 UTC  
> Url: https://github.com/boostorg/boost/issues/195  

Hello dear team of Boostorg.  
  
First, I am not sure wheter this is the right repository to post this issue, if not, I will grandly repost it somewhere else.  
  
The error is pretty much the same as in the closed issue [#9438](https://svn.boost.org/trac10/ticket/9438#no1) and I am not sure why it reappeared.  
  
- OS: Ubuntu 18.04 Bionic Beaver  
- clang: 6.0  
- lemon: 1.3.1  
- boost: happened under 1.63 and  was reproduced with boost 1.68  
  
```c++  
#include <boost/geometry.hpp>  
#include <lemon/list_graph.h>  
  
namespace bg = boost::geometry;  
namespace bgi = boost::geometry::index;  
  
using Point = boost::geometry::model::point<double, 2, bg::cs::cartesian>;  
using Graph = lemon::ListDigraph;  
using Node = lemon::ListDigraph::Node;  
typedef std::pair<Point, Node> Pair;  
using BoostRTree = bgi::rtree<Pair, bgi::quadratic<16>>;  
using BoostQueryResult = std::vector<Pair>;  
  
int main()  
{  
	Point one = Point(10, 51);  
	Point two = Point(10, 51);  
	BoostRTree rtreePair = BoostRTree();  
	Graph graph;  
	Node dummy1 = graph.addNode();  
	Node dummy2 = graph.addNode();  
  
	rtreePair.insert(std::make_pair(one, dummy1));	  
	return 0;  
}  
  
```

---

## Comment 1

> Username: mclow  
> Created at: 2018-09-21 11:55:04 UTC  
> Url: https://github.com/boostorg/boost/issues/195#issuecomment-423506319  

Actually, a better place would be the Boost.Graph bug list: https://github.com/boostorg/graph/issues

---

## Comment 2

> Username: Magnati  
> Created at: 2018-09-21 13:11:17 UTC  
> Url: https://github.com/boostorg/boost/issues/195#issuecomment-423525721  

Placed it there. Thank you. :)
