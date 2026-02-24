# #80 - boost::polygon::area returns 0 area [Closed]

> Username: dratchkov  
> Created at: 2022-06-26 19:07:35 UTC  
> Updated at: 2022-06-26 21:39:47 UTC  
> Closed at: 2022-06-26 21:39:22 UTC  
> Url: https://github.com/boostorg/polygon/issues/80  

Hi,  
  
I am trying to the area of small rectangular polygon. For some reason `boost::polygon::area` return 0. Code sample is below  
  
```  
#include <boost/polygon/polygon.hpp>  
#include <boost/polygon/rectangle_data.hpp>  
#include <boost/polygon/polygon_set_data.hpp>  
#include <boost/polygon/polygon_data.hpp>  
#include <boost/version.hpp>  
#include <boost/format.hpp>  
  
template<typename T>  
auto make_rect(T x1, T y1, T x2, T y2) {  
    auto r = boost::polygon::rectangle_data(x1, y1, x2, y2);  
    boost::polygon::polygon_data<T> p;  
    boost::polygon::assign(p, r);  
    return p;  
}  
  
int main(int argc, char **argv) {  
    std::cout << BOOST_LIB_VERSION << std::endl;  
    boost::polygon::polygon_set_data<double> poly, poly1;  
    poly.insert(make_rect(0.003065, 0.0007, 0.0034, 0.0009525));  
    std::cout << boost::format("%e") % boost::polygon::area(poly) << std::endl;  
}  
```  
  
The result of the above code is:  
```  
1_78  
0.000000e+00  
```  
  
Is this a bug or I am missing a `#define`?  
  
Not knowing how useful this is:  
I did a little bit of debugging, stepping side by side through "good" and "bad" test cases. First, "clean"-inig the polygon produces different results. Seems like cleaning is done by running a boolean op with nothing, and then this code inside `boost/polygon/detail/scan_arbitrary.hpp`:  
```  
  
    //iterator over range of vertex property elements and call result functor  
    //passing edge to be output, the merged data on both sides and the result  
    template <typename result_type, typename result_functor, typename iT>  
    void scan(result_type& result, result_functor rf, iT begin, iT end) {  
      while(begin != end) {  
        x_ = (*begin).first.first.get(HORIZONTAL); //update scanline stop location  
        //print_scanline();  
        --x_;   // <<---- NOTE THIS  
        remove_retired_edges_from_scanline();  
        ++x_; // <<-- NOTE THIS  
        begin = handle_input_events(result, rf, begin, end);  
        remove_retired_edges_from_scanline();  
        //print_scanline();  
        insert_new_edges_into_scanline();  
      }  
      //print_scanline();  
      x_ = (std::numeric_limits<Unit>::max)();  
      remove_retired_edges_from_scanline();  
    }  
  
```  
  
Is the code assuming that the sequence `--x_` followed by `++x_` will produce `x_`? In the case `x_ = 0.003065` the final result is `0.0030649999999999844`

---

## Comment 1

> Username: dratchkov  
> Created at: 2022-06-26 21:34:41 UTC  
> Url: https://github.com/boostorg/polygon/issues/80#issuecomment-1166651583  

Should've seen this sooner in the documentation. Duh.  
  
> The coordinate data type is a template parameter of all **data types** and algorithms provided by the library, and is **expected to be integral**.  **Floating point coordinate data types are not supported** by the algorithms implemented in the library due to the fact that the achieving floating point robustness implies a different set of algorithms and generally platform specific assumptions about floating point representations.

---

## Comment 2

> Username: dratchkov  
> Created at: 2022-06-26 21:39:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/80#issuecomment-1166652556  

See previous comment
