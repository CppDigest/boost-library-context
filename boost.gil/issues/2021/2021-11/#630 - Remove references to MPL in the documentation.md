# #630 - Remove references to MPL in the documentation [Closed]

> Username: sdebionne  
> Created at: 2021-11-08 09:29:14 UTC  
> Updated at: 2021-11-08 18:25:53 UTC  
> Closed at: 2021-11-08 18:25:53 UTC  
> Url: https://github.com/boostorg/gil/issues/630  

### Actual behavior  
  
Follow up of #629   
  
```  
$ git grep -w mpl -- doc  
doc/design/color_base.rst:  // Metafunction returning an mpl::int_ equal to the number of elements in the color base  
doc/design/color_space.rst:  rgba_t = using mpl::vector4<red_t, green_t, blue_t, alpha_t>;  
doc/design/color_space.rst:      typename ChannelMapping = mpl::range_c<int, 0, mpl::size<ColorSpace>::value>  
doc/design/color_space.rst:  using bgra_layout_t = layout<rgba_t, mpl::vector4_c<int,2,1,0,3>>;  
doc/design/color_space.rst:  using argb_layout_t = layout<rgba_t, mpl::vector4_c<int,1,2,3,0>>;  
doc/design/color_space.rst:  using abgr_layout_t = layout<rgba_t, mpl::vector4_c<int,3,2,1,0>>;  
doc/design/metafunctions.rst:  using VT = typename derived_view_type<View, boost::use_default, gray_t, mpl::true_>::type;  
doc/design/pixel.rst:  typedef packed_pixel_type<uint16_t, mpl::vector3_c<unsigned,5,6,5>, rgb_layout_t>::type rgb565_pixel_t;  
doc/design/pixel.rst:  typedef packed_pixel_type<uint16_t, mpl::vector3_c<unsigned,5,6,5>, bgr_layout_t>::type bgr556_pixel_t;  
doc/design/pixel.rst:  typedef const bit_aligned_pixel_reference<unsigned char, mpl::vector3_c<unsigned,2,3,2>, bgr_layout_t, true>  bgr232_ref_t;  
doc/design/pixel.rst:  BOOST_MPL_ASSERT((is_same<channel_mapping_type<bgr8_pixel_t>::type, mpl::vector3_c<int,2,1,0> > ));  
doc/design/pixel_iterator.rst:    where SameType<is_iterator_adaptor<Iterator>::type, mpl::true_>;  
doc/io.rst:    typedef mpl::vector< gray8_image_t  
doc/io.rst:    typedef mpl::vector< gray8_image_t  
```  
  
### Expected  behavior  
  
The documentation needs to be updated.

---

## Comment 1

> Username: mloskot  
> Created at: 2021-11-08 18:24:59 UTC  
> Url: https://github.com/boostorg/gil/issues/630#issuecomment-963439642  

Thanks for catching this
