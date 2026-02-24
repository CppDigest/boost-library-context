# #117 - 9438-returns: call of overloaded ‘ignore_unused_variable_warning(...)’ is ambiguous [Closed]

> Username: Magnati  
> Created at: 2018-09-21 13:10:55 UTC  
> Updated at: 2018-10-15 17:03:53 UTC  
> Closed at: 2018-10-15 17:03:53 UTC  
> Url: https://github.com/boostorg/graph/issues/117  

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

> Username: anadon  
> Created at: 2018-09-25 14:37:54 UTC  
> Url: https://github.com/boostorg/graph/issues/117#issuecomment-424369768  

I don't think graph is the right place for this.  It looks like you're actually looking for boost:geometry.

---

## Comment 2

> Username: Magnati  
> Created at: 2018-10-08 11:26:36 UTC  
> Url: https://github.com/boostorg/graph/issues/117#issuecomment-427799318  

Okay, I will try it there.

---

## Comment 3

> Username: anadon  
> Created at: 2018-10-08 11:32:34 UTC  
> Url: https://github.com/boostorg/graph/issues/117#issuecomment-427800658  

Let me know how that goes so I can let a proper maintainer close the ticket if that is the right place.

---

## Comment 4

> Username: Magnati  
> Created at: 2018-10-15 12:45:05 UTC  
> Url: https://github.com/boostorg/graph/issues/117#issuecomment-429838950  

I was at [boost/geometry](https://github.com/boostorg/geometry/issues/513#issuecomment-429272962) , now I am at [boost/range](https://github.com/boostorg/range/issues/78).  
For now it seems that indeed `boost/graph` is not the right place.

---

## Comment 5

> Username: anadon  
> Created at: 2018-10-15 14:42:43 UTC  
> Url: https://github.com/boostorg/graph/issues/117#issuecomment-429881265  

@jzmaddock Close 'er out!
