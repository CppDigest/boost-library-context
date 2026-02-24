# #1445 - Cannot be used with `Boost.Sort` because `boost::range::sort` is used internally [Open]

> Username: AlanIWBFT  
> Created at: 2026-01-07 12:24:48 UTC  
> Updated at: 2026-01-07 12:24:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1445  

The following snippet doesn't compile:  
https://godbolt.org/z/1TdazEzjT  
```c++  
#include <boost/geometry.hpp>  
#include <boost/sort/sort.hpp>  
  
int main()  
{  
    return 0;  
}  
```  
  
output:  
```  
In file included from /app/boost/include/boost/sort/spreadsort/integer_sort.hpp:25,  
                 from /app/boost/include/boost/sort/spreadsort/spreadsort.hpp:27,  
                 from /app/boost/include/boost/sort/sort.hpp:16,  
                 from <source>:2:  
/app/boost/include/boost/sort/spreadsort/detail/constants.hpp:12:11: error: 'namespace boost::sort { }' conflicts with a previous declaration  
   12 | namespace sort {  
      |           ^~~~  
In file included from /app/boost/include/boost/detail/algorithm.hpp:47,  
                 from /app/boost/include/boost/graph/graph_utility.hpp:27,  
                 from /app/boost/include/boost/graph/biconnected_components.hpp:22,  
                 from /app/boost/include/boost/geometry/algorithms/detail/overlay/graph/graph_util.hpp:12,  
                 from /app/boost/include/boost/geometry/algorithms/detail/overlay/graph/detect_biconnected_components.hpp:19,  
                 from /app/boost/include/boost/geometry/algorithms/detail/overlay/traverse.hpp:20,  
                 from /app/boost/include/boost/geometry/algorithms/detail/overlay/overlay.hpp:37,  
                 from /app/boost/include/boost/geometry/algorithms/detail/overlay/intersection_insert.hpp:34,  
                 from /app/boost/include/boost/geometry/algorithms/detail/intersection/interface.hpp:18,  
                 from /app/boost/include/boost/geometry/algorithms/detail/intersection/gc.hpp:19,  
                 from /app/boost/include/boost/geometry/algorithms/difference.hpp:19,  
                 from /app/boost/include/boost/geometry/algorithms/detail/relate/implementation_gc.hpp:18,  
                 from /app/boost/include/boost/geometry/algorithms/detail/covered_by/implementation_gc.hpp:16,  
                 from /app/boost/include/boost/geometry/algorithms/covered_by.hpp:25,  
                 from /app/boost/include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp:33,  
                 from /app/boost/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp:30,  
                 from /app/boost/include/boost/geometry/algorithms/detail/buffer/implementation.hpp:25,  
                 from /app/boost/include/boost/geometry/algorithms/buffer.hpp:22,  
                 from /app/boost/include/boost/geometry/geometry.hpp:63,  
                 from /app/boost/include/boost/geometry.hpp:17,  
                 from <source>:1:  
/app/boost/include/boost/range/algorithm/sort.hpp:30:27: note: previous declaration 'namespace boost::range { }::sort'  
   30 | inline RandomAccessRange& sort(RandomAccessRange& rng)  
      |                           ^~~~  
In file included from /app/boost/include/boost/sort/spreadsort/detail/spreadsort_common.hpp:24,  
                 from /app/boost/include/boost/sort/spreadsort/detail/integer_sort.hpp:24,  
                 from /app/boost/include/boost/sort/spreadsort/integer_sort.hpp:26:  
/app/boost/include/boost/sort/pdqsort/pdqsort.hpp:29:11: error: 'namespace boost::sort { }' conflicts with a previous declaration  
   29 | namespace sort {  
      |           ^~~~  
/app/boost/include/boost/range/algorithm/sort.hpp:30:27: note: previous declaration 'namespace boost::range { }::sort'  
   30 | inline RandomAccessRange& sort(RandomAccessRange& rng)  
      |                           ^~~~  
/app/boost/include/boost/sort/spreadsort/detail/spreadsort_common.hpp:29:11: error: 'namespace boost::sort { }' conflicts with a previous declaration  
   29 | namespace sort {  
      |           ^~~~  
/app/boost/include/boost/range/algorithm/sort.hpp:30:27: note: previous declaration 'namespace boost::range { }::sort'  
   30 | inline RandomAccessRange& sort(RandomAccessRange& rng)  
      |                           ^~~~  
...  
```
