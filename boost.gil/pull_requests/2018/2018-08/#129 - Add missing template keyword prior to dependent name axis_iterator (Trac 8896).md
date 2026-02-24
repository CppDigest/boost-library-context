# #129 Add missing template keyword prior to dependent name axis_iterator (Trac 8896) [Merged]

> Username: mloskot  
> Created at: 2018-08-23 13:58:50 UTC  
> Updated at: 2018-10-30 17:19:48 UTC  
> Merged at: 2018-08-27 07:10:47 UTC  
> Closed at: 2018-08-27 07:10:47 UTC  
> Url: https://github.com/boostorg/gil/pull/129  

### Description  
  
* Apply patch from https://svn.boost.org/trac10/ticket/8896  
* Add compile-time test of `RandomAccessNDImageViewConcept`  
  * Confirms the reported failure (tested with GCC 7.3 and clang 5.0)  
  * Verifies correctness of the patch  
  
### Environment  
  
All relevant information like:  
  
- Boost version (see `<boost/version.hpp>`): 1.68  
- Compiler version: GCC 4.2 or later, clang 3.8 or later  
- Build settings:  
  
### References  
  
https://svn.boost.org/trac10/ticket/8896  
  
### Tasklist  
  
- [x] Review (@chhenning & @stefanseefeld could you review the change, please?)  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
The MWE to reproduce the problem originally posted in the Trac ticket:  
  
```  
mloskot@xenial:~/tmp$ cat gil_ticket_8896.cpp  
#include <iostream>  
#include <boost/gil/gil_all.hpp>  
using namespace boost;  
namespace bg = boost::gil;  
int main(int argc, char **argv)  
{  
    bg::gray8_image_t img(2,2);  
    bg::gray8_view_t vw(bg::view(img));  
    bg::gray8_view_t::point_t p(0,0);  
    bg::gray8_view_t::axis<0>::iterator iter = vw.template axis_iterator<0>(p);  
  
    std::cout << (*iter)[0] << std::endl;  
    return 0;  
}  
```  
  
* Compiled with GCC 7.3.0  
  
```  
mloskot@xenial:~/tmp$ g++ -std=c++11 -I/mnt/d/boost.wsl  gil_ticket_8896.cpp  
In file included from /mnt/d/boost.wsl/boost/gil/algorithm.hpp:32:0,  
                 from /mnt/d/boost.wsl/boost/gil/gil_all.hpp:27,  
                 from gil_ticket_8896.cpp:2:  
/mnt/d/boost.wsl/boost/gil/image_view.hpp: In instantiation of ‘typename boost::gil::image_view<L>::axis<D>::iterator boost::gil::image_view<L>::axis_iterator(const point_t&) const [with long unsigned int D = 0; Loc = boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t> > >*> >; typename boost::gil::image_view<L>::axis<D>::iterator = boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t> > >*; boost::gil::image_view<L>::point_t = boost::gil::point2<long int>]’:  
gil_ticket_8896.cpp:10:69:   required from here  
/mnt/d/boost.wsl/boost/gil/image_view.hpp:139:125: error: invalid operands of types ‘<unresolved overloaded function type>’ and ‘long unsigned int’ to binary ‘operator<’  
     template <std::size_t D> typename axis<D>::iterator axis_iterator(const point_t& p) const { return _pixels.axis_iterator<D>(p); }  
                                                                                                        ~~~~~~~~~~~~~~~~~~~~~^~  
```  
  
* Compiled with clang 5.0.0  
  
```  
mloskot@xenial:~/tmp$ clang++ -std=c++11 -I/mnt/d/boost.wsl  gil_ticket_8896.cpp  
In file included from gil_ticket_8896.cpp:2:  
In file included from /mnt/d/boost.wsl/boost/gil/gil_all.hpp:27:  
In file included from /mnt/d/boost.wsl/boost/gil/algorithm.hpp:32:  
/mnt/d/boost.wsl/boost/gil/image_view.hpp:139:112: error: missing 'template' keyword prior to dependent template name 'axis_iterator'  
    template <std::size_t D> typename axis<D>::iterator axis_iterator(const point_t& p) const { return _pixels.axis_iterator<D>(p); }  
                                                                                                               ^  
gil_ticket_8896.cpp:10:51: note: in instantiation of function template specialization 'boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::pixel<unsigned char,  
      boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t>, boost::mpl::range_c<int, 0, 1> > > *> > >::axis_iterator<0>' requested here  
    bg::gray8_view_t::axis<0>::iterator iter = vw.axis_iterator<0>(p);  
                                                  ^  
1 error generated.  
```  
  
The patch fixes the both compilations.

---

## Review 1 [Approved]

> Username: chhenning  
> Created_at: 2018-08-24 13:04:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/129#pullrequestreview-149294331  

Good stuff!

---
