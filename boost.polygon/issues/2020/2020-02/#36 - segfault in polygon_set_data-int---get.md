# #36 - segfault in polygon_set_data<int>::get [Open]

> Username: tvf  
> Created at: 2020-02-23 13:35:22 UTC  
> Updated at: 2020-04-19 19:51:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/36  

The following program crashes with a segmentation fault inside the `get()` call.  I've tested against `1.65`, `1.72`, and current `master`.  I'm pretty sure that this usage falls inside the API contract, but perhaps I'm missing something?  
  
```c++  
#include <boost/polygon/polygon.hpp>  
  
int main(int argc, char **argv) {  
    const boost::polygon::point_data<int> triangles[][3] = {  
        {{-7273739,1362950}, {-7273144,1362356}, {-3077455,-2833333}},  
        {{-490017,-3840545}, {-2888380,-1442182}, {-6168936,1838374}},  
        {{-3078718,-2832071}, {-3078274,-2832514}, {-2288161,-2042401}},  
    };  
  
    boost::polygon::polygon_set_data<int> polygon_set;  
    polygon_set.insert(boost::polygon::polygon_data<int>(std::begin(triangles[0]), std::end(triangles[0])));  
    polygon_set.insert(boost::polygon::polygon_data<int>(std::begin(triangles[1]), std::end(triangles[1])));  
    polygon_set.insert(boost::polygon::polygon_data<int>(std::begin(triangles[2]), std::end(triangles[2])));  
  
    std::vector<boost::polygon::polygon_with_holes_data<int>> output;  
    polygon_set.get(output);  
}  
```

---

## Comment 1

> Username: tvf  
> Created at: 2020-02-23 13:40:04 UTC  
> Url: https://github.com/boostorg/polygon/issues/36#issuecomment-590069982  

I'm using GCC 7.4 on Ubuntu 18.04.4 LTS.  Building with `-fsanitize=address` reports a stack buffer overflow.

---

## Comment 2

> Username: dmsteck  
> Created at: 2020-04-19 19:45:59 UTC  
> Url: https://github.com/boostorg/polygon/issues/36#issuecomment-616214070  

I investigated this and the root cause of the problem lies in `polygon_set_data<int>::clean()`. There's a specific issue of two of the input line segments where `compute_exact_intersection` fails to, well, compute the exact intersection. This results in inconsistent geometry/violated preconditions of other functions and becomes a segfault way down the line.  
  
It turns out that this kind of behaviour is expected (see https://lists.boost.org/boost-users/2010/11/63944.php) because the default implementation of `compute_exact_intersection` uses `long double` to compute the intersection points, which can produce incorrectly rounded results in certain cases.  
  
A simple solution is to link the library against GMP by using the `gmp_override.hpp` header. This fixes the example presented here.

---

## Comment 3

> Username: dmsteck  
> Created at: 2020-04-19 19:50:11 UTC  
> Updated at: 2020-04-19 19:51:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/36#issuecomment-616214635  

I think there are two approaches to prevent this kind of problem for users of the library:  
* Make it more obvious that the library should be used with GMP, and make it easier to use the library in that way. Including `gmp_override.hpp` whenever you include `polygon.hpp` is error-prone. A simple solution would be to provide a feature macro that automatically configures the library for this kind of usage.  
* A more ambitious alternative would be to attempt to fix `compute_exact_intersection` in its default state, i.e., using `long double`. It is relatively easy to come up with a quick win here (and my prototype fixes the segfault in this example), but I'm not sure/haven't analysed whether the issue can be fixed for all possible input values, and in a portable way.  
  
@asydorchuk Could you let me know which of these solutions (if any) you would consider for inclusion in the library? I would be happy to raise a PR. Thank you.
