# #513 - 9438-returns: call of overloaded ‘ignore_unused_variable_warning(...)’ is ambiguous [Closed]

> Username: Magnati  
> Created at: 2018-10-08 11:27:06 UTC  
> Updated at: 2018-10-15 12:35:13 UTC  
> Closed at: 2018-10-12 18:08:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/513  

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

> Username: mloskot  
> Created at: 2018-10-08 12:49:06 UTC  
> Updated at: 2018-10-08 12:49:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/513#issuecomment-427819521  

FYI, a `lemon`-stripped version of your example:  
  
```cpp  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
namespace bgi = boost::geometry::index;  
  
using Point = boost::geometry::model::point<double, 2, bg::cs::cartesian>;  
struct Node {};  
struct Graph { Node addNode() { return Node(); } };  
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
  
It does not seem to reproduce the problem using Boost.Geometry from the Boost 1.68.0, its `master`and `develop` branches. Tried clang 6.0 and GCC 8.2

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-10-08 17:50:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/513#issuecomment-427923048  

In all cases `boost` namespace is specified in Boost.Geometry.  It could help if you pasted the compiler output or say in which file exactly the ambiguous call of `ignore_unused_variable_warning` is detected.  
  
Side note: instead of using `boost::ignore_unused_variable_warning` from ConceptCheck we should use `boost::ignore_unused` from Core consistently. We use both now.

---

## Comment 3

> Username: mloskot  
> Created at: 2018-10-12 09:58:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/513#issuecomment-429272962  

@awulkiew See #514

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-10-12 13:31:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/513#issuecomment-429326584  

@mloskot Thanks!  
  
Still I think the problem @Magnati has is not caused by Boost.Geometry. He didn't write anything more so it's impossible to know for sure but with your improvement it should definitely not cause any problems. So I'm closing this issue

---

## Comment 5

> Username: mloskot  
> Created at: 2018-10-12 13:36:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/513#issuecomment-429328018  

Yes, I agree. I just wanted to clear any possibility for this issue to be caused by Boost.Geometry.

---

## Comment 6

> Username: Magnati  
> Created at: 2018-10-15 10:25:52 UTC  
> Updated at: 2018-10-15 12:35:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/513#issuecomment-429792752  

Wow, that was fast.  
I'm sorry, I wired my head around other things last week.  
  
The problem on boost side is indeed coming from `boost/concept_check.hpp`:  
`boost/concept_check.hpp:52:34`  
`boost/concept_check.hpp:178:37`  
  
On lemon side it is the equivalent.   
`lemon/concept_check.h:37:34`  
  
So I hope the #514 did the job.  
  
Thanks for your fast interaction, @mloskot, @awulkiew, even if it was too fast for me.  
  
Edit:   
Well now its `/boost/range/concepts.hpp:19:0` which makes it ambigous.   
Thank you here, I will go there.
