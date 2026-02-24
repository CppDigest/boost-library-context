# #354 - [ASL] Should fill_pixels work with the view produced by subsampled_view? [Open]

> Username: mloskot  
> Created at: 2019-07-26 19:02:39 UTC  
> Updated at: 2019-07-26 19:05:22 UTC  
> Url: https://github.com/boostorg/gil/issues/354  

Report copied from Adobe Source Libraries (ASL) discussion forum https://sourceforge.net/p/adobe-source/discussion/648138/thread/c94ac980/  
  
------  
  
Should `fill_pixels` work with the view produced by `subsampled_view`?  
It seems `memory_based_step_iterator` does not model a `PixelIterator`.  
  
For example, this does not compile:  
  
```  
#include <iostream>  
#include <boost/gil/gil_all.hpp>  
  
using namespace boost;  
using namespace gil;  
  
int main(int argc, char **argv) {  
    rgb8_pixel_t black(0,0,0);  
    rgb8_planar_image_t img(8,8,black, 0);  
  
    dynamic_xy_step_type<rgb8_planar_view_t>::type v(subsampled_view(view(img), 1, 2));  
    rgb8_pixel_t white(255,255,255);  
    fill_pixels(v, white);  
    return 0;  
}  
```  
  
```  
c++ -Wall -I/usr/local/include -c fillpix.cpp  
/usr/local/include/boost/gil/color_base_algorithm.hpp: In instantiation of 'boost::gil::size<boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > > >':  
/usr/local/include/boost/gil/color_base_algorithm.hpp:673:   instantiated from 'Op boost::gil::static_for_each(P1&, P2&, const P3&, Op) [with P1 = boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, P2 = boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, P3 = boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > >, Op = boost::gil::detail::std_fill_t]'  
/usr/local/include/boost/gil/algorithm.hpp:404:   instantiated from 'void boost::gil::detail::fill_aux(It, It, const P&, mpl_::true_) [with It = boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, P = boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > >]'  
/usr/local/include/boost/gil/algorithm.hpp:419:   instantiated from 'void boost::gil::fill_pixels(const View&, const Value&) [with View = boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > > > > >, Value = boost::gil::rgb8_pixel_t]'  
fillpix.cpp:13:   instantiated from here  
/usr/local/include/boost/gil/color_base_algorithm.hpp:58: error: no type named 'layout_t' in 'class boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >'  
/usr/local/include/boost/gil/color_base_algorithm.hpp: In function 'Op boost::gil::static_for_each(P1&, P2&, const P3&, Op) [with P1 = boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, P2 = boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, P3 = boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > >, Op = boost::gil::detail::std_fill_t]':  
/usr/local/include/boost/gil/algorithm.hpp:404:   instantiated from 'void boost::gil::detail::fill_aux(It, It, const P&, mpl_::true_) [with It = boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, P = boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > >]'  
/usr/local/include/boost/gil/algorithm.hpp:419:   instantiated from 'void boost::gil::fill_pixels(const View&, const Value&) [with View = boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boost::gil::bits8*, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > > > > >, Value = boost::gil::rgb8_pixel_t]'  
fillpix.cpp:13:   instantiated from here  
/usr/local/include/boost/gil/color_base_algorithm.hpp:673: error: 'value' is not a member of 'boost::gil::size<boost::gil::memory_based_step_iterator<boost::gil::planar_pixel_iterator<boos  
```  
  
### Tasklist  
  
- [ ] Confirm as by-design or a bug  
- [ ] Fix, if a bug  
- [ ] Add tests
