# #547 - Undefined behavior in rtree constructed with zip_iterator (boost 1.69.0) [Closed]

> Username: Caian  
> Created at: 2019-01-21 15:27:09 UTC  
> Updated at: 2019-01-25 19:19:39 UTC  
> Closed at: 2019-01-25 19:19:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/547  

**Boost version: 1.69.0**  
**gcc version 4.8.4**  
  
I'm trying to construct a `boost::geometry::index::rtree` from two iterators, one containing locations and a second one containing the data. This is a restriction from the code I'm using. For that i'm using a `boost::zip_iterator` so I can pass the resulting iterator to the constructor of the rtree.  
  
Unfortunately I'm having unpredictable results when doing nearest neighbor queries: Queries for a position that I know it's in the tree are returning completely different results.  
  
I have tested the code in 3 different machines and running with GDB "fixes" the problem in some of them (heisenbug).  
  
This is a minimal working example of the bug, compiled with `g++ -std=c++98 -O0 -g -rdynamic -Wall -Wextra -Wconversion example.cpp -o example -ldl -lbacktrace`:  
  
```c++  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/index/rtree.hpp>  
#include <boost/iterator/zip_iterator.hpp>  
#include <boost/iterator/transform_iterator.hpp>  
#include <boost/tuple/tuple.hpp>  
  
#include <algorithm>  
#include <iostream>  
#include <vector>  
#include <cmath>  
#include <set>  
  
#include <stdlib.h>  
  
int main(int argc, const char* argv[])  
{  
    (void)argc;  
    (void)argv;  
  
    //////////////////////////////////////////////////////////////////////////  
  
    using namespace boost::geometry;  
  
    typedef index::quadratic<16> params_t;  
    typedef cs::cartesian coordsys_t;  
  
    typedef model::point<float, 2, coordsys_t> my_point;  
    typedef std::pair<int, int> my_data;  
    typedef boost::tuple<my_point, my_data> my_node;  
  
    typedef boost::geometry::index::rtree<my_node, params_t> my_tree;  
  
    //////////////////////////////////////////////////////////////////////////  
  
    const int N = 4000;  
  
    std::vector<my_data> node_data(N);  
    std::vector<my_point> node_loc(N);  
    std::vector<my_node> node_comb(N);  
  
    for (int i = 0; i < N; i++) {  
  
        float fi = static_cast<float>(i);  
        float fN = static_cast<float>(N);  
        float x = (float)((fN - 0.8f*fi)*cos(fi/100));  
        float y = (float)((fN - 0.8f*fi)*sin(fi/100));  
  
        node_loc[i] = my_point(100 * x, 100 * y);  
        node_data[i] = std::make_pair(i, 0);  
        node_comb[i].template get<0>() = node_loc[i];  
        node_comb[i].template get<1>() = node_data[i];  
    }  
  
    //////////////////////////////////////////////////////////////////////////  
  
    my_tree rtree(  
        boost::make_zip_iterator(  
            boost::make_tuple(node_loc.begin(), node_data.begin())),  
        boost::make_zip_iterator(  
            boost::make_tuple(node_loc.end(), node_data.end()))  
    );  
  
    // Comment the lines above and uncomment the next line to make it work  
    //my_tree rtree(node_comb.begin(), node_comb.end());  
  
    //////////////////////////////////////////////////////////////////////////  
  
    const size_t Who = 1337;  
  
    typedef my_tree::const_query_iterator iterator;  
  
    my_point point2 = node_loc[Who];  
  
    iterator begin = rtree.qbegin(boost::geometry::index::nearest(point2, 1));  
    iterator end = rtree.qend();  
  
    if (begin == end) {  
        std::cerr  
            << "Error: Not found!"  
            << std::endl;  
        return 1;  
    }  
  
    const my_node& node = *begin;  
    const my_point& pointq = node.template get<0>();  
    const my_data& whoq = node.template get<1>();  
  
    if (++begin != end) {  
        std::cerr  
            << "Error: Multiple results!"  
            << std::endl;  
        return 1;  
    }  
  
    std::cout  
        << "Asked: "  
            << Who << " ("  
            << point2.template get<0>() << ", "  
            << point2.template get<1>() << ")"  
            << std::endl  
        << "Got: "  
            << whoq.first << " ("  
            << pointq.template get<0>() << ", "  
            << pointq.template get<1>() << ")"  
            << std::endl;  
  
    return 0;  
}  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-01-22 02:45:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/547#issuecomment-456252427  

Thanks for the report. Could you check if passing the following `my_indexable` struct as IndexableGetter fixes the issue?  
  
    template <typename Point>  
    struct my_indexable  
    {  
        typedef Point const& result_type;  
  
        template <typename Value>  
        result_type operator()(Value const& v) const  
        {  
            return boost::get<0>(v);  
        }  
    };  
  
    typedef model::point<float, 2, coordsys_t> my_point;  
    typedef std::pair<int, int> my_data;  
    typedef boost::tuple<my_point, my_data> my_node;  
  
    typedef boost::geometry::index::rtree<my_node, params_t, my_indexable<my_point> > my_tree;

---

## Comment 2

> Username: Caian  
> Created at: 2019-01-22 12:57:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/547#issuecomment-456389898  

Hi @awulkiew I can confirm that your modification fixes the issue. Thank you.  
  
I went for the default indexer because the documentation states it should work with `boost::tuple`:  
  
```  
The default IndexableGetter can translate all types adapted to Point, Box or Segment   
concepts (called Indexables). Furthermore, it can handle std::pair<Indexable, T>, boost::tuple<Indexable, ...> and std::tuple<Indexable, ...> when possible. For example, for Value   
of type std::pair<Box, int>, the default IndexableGetter translates from std::pair<Box, int> const&   
to Box const&.  
```  
https://www.boost.org/doc/libs/1_69_0/libs/geometry/doc/html/geometry/reference/spatial_indexes/boost__geometry__index__rtree.html

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-01-22 13:29:48 UTC  
> Updated at: 2019-01-22 18:31:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/547#issuecomment-456399354  

Right, it's understandable. The problem is that `zip_iterator` returns non-true reference of type: `tuples::cons<my_point&, tuples::cons<my_data&, tuples::null_type>>` which is convertible to ValueType. This tuple-reference is converted in default indexer's `operator()` to ValueType and reference to a member (my_point) of this temporary object is returned.  
  
So I think that I'll fix it by adding operator() overload to the default indexer similar to the one in `my_indexable` above. Returning the Indexable by value/copy is probably not necessary.

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-01-25 00:18:55 UTC  
> Updated at: 2019-01-25 00:19:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/547#issuecomment-457408211  

The default `index::indexable<>` should work now. I also made sure that implicit conversions won't happen. Fix: https://github.com/boostorg/geometry/commit/e090c027d0e3ef622c65296313cab4ba74133821. If you want to try it out you have to use develop branch. If you have any suggestions let me know. Can I close this issue?

---

## Comment 5

> Username: Caian  
> Created at: 2019-01-25 18:39:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/547#issuecomment-457676242  

I have replaced the geometry directory from boost 1.69.0 with the one from your commit and the test case is working properly.  
  
Also the commit seems OK.  
  
So I believe the issue can be closed.  
  
Thanks.
