# #781 - [docs/example] example on how to interoperate with code expecting pixels as raw data [Open]

> Username: planetmarshall  
> Created at: 2025-12-08 14:58:46 UTC  
> Updated at: 2025-12-08 22:48:28 UTC  
> Url: https://github.com/boostorg/gil/issues/781  

A common task is to interoperate with code expecting data as raw bytes, eg as a single contiguous array or as an array of padded scanlines.  
  
It would be great to have an example and an entry in the docs.  
  
I'd be happy to make a contribution but the reason I'm asking about it is that I'm not exactly sure how best to do it, short of just copying pixel-by-pixel.

---

## Comment 1

> Username: sdebionne  
> Created at: 2025-12-08 18:12:44 UTC  
> Url: https://github.com/boostorg/gil/issues/781#issuecomment-3628382916  

I believe that what you are looking for is documented here:  
  
https://www.boost.org/doc/libs/latest/libs/gil/doc/html/reference/group___image_view_constructors.html  
  
Does that fit your needs ?

---

## Comment 2

> Username: planetmarshall  
> Created at: 2025-12-08 18:25:16 UTC  
> Url: https://github.com/boostorg/gil/issues/781#issuecomment-3628436917  

> I believe that what you are looking for is documented here:  
>   
> https://www.boost.org/doc/libs/latest/libs/gil/doc/html/reference/group___image_view_constructors.html  
>   
> Does that fit your needs ?  
  
Possibly. Unfortunately that documentation is not readable for me. Possibly something to do with the stylesheet?

---

## Comment 3

> Username: planetmarshall  
> Created at: 2025-12-08 18:34:48 UTC  
> Url: https://github.com/boostorg/gil/issues/781#issuecomment-3628500589  

>   
> Possibly. Unfortunately that documentation is not readable for me. Possibly something to do with the stylesheet?  
  
Raised a separate issue #782

---

## Comment 4

> Username: sdebionne  
> Created at: 2025-12-08 20:58:26 UTC  
> Url: https://github.com/boostorg/gil/issues/781#issuecomment-3628976340  

Indeed this is unfortunate.  
  
```cpp  
// from raw CMYK planar data:  
  
template<typename IC >  
auto planar_cmyk_view (std::size_t width, std::size_t height, IC c, IC m, IC y, IC k, std::ptrdiff_t rowsize_in_bytes) -> typename type_from_x_iterator< planar_pixel_iterator< IC, cmyk_t > >::view_t  
   
// from 2-channel planar data:  
  
template<typename IC >  
auto planar_devicen_view (std::size_t width, std::size_t height, IC c0, IC c1, std::ptrdiff_t rowsize_in_bytes) -> typename type_from_x_iterator< planar_pixel_iterator< IC, devicen_t< 2 > > >::view_t  
   
// from 3-channel planar data:  
  
template<typename IC >  
auto planar_devicen_view (std::size_t width, std::size_t height, IC c0, IC c1, IC c2, std::ptrdiff_t rowsize_in_bytes) -> typename type_from_x_iterator< planar_pixel_iterator< IC, devicen_t< 3 > > >::view_t  
  
...  
  
// Constructing image views from raw interleaved pixel data:  
   
template<typename Iterator >  
auto interleaved_view (std::size_t width, std::size_t height, Iterator pixels, std::ptrdiff_t rowsize_in_bytes) -> typename type_from_x_iterator< Iterator >::view_t  
   
template<typename Iterator >  
auto interleaved_view (point< std::ptrdiff_t > dim, Iterator pixels, std::ptrdiff_t rowsize_in_bytes) -> typename type_from_x_iterator< Iterator >::view_t  
   
// Returns C pointer to the the channels of an interleaved homogeneous view:  
  
template<typename HomogeneousView >  
auto interleaved_view_get_raw_data (HomogeneousView const &view) -> typename detail::channel_pointer_type< HomogeneousView >::type  
   
template<typename HomogeneousView >  
auto planar_view_get_raw_data (HomogeneousView const &view, int plane_index) -> typename detail::channel_pointer_type< HomogeneousView >::type  
   
// from raw RGB planar data:  
  
template<typename IC >  
auto planar_rgb_view (std::size_t width, std::size_t height, IC r, IC g, IC b, std::ptrdiff_t rowsize_in_bytes) -> typename type_from_x_iterator< planar_pixel_iterator< IC, rgb_t > >::view_t  
   
// from raw RGBA planar data:  
  
template<typename ChannelPtr >  
auto planar_rgba_view (std::size_t width, std::size_t height, ChannelPtr r, ChannelPtr g, ChannelPtr b, ChannelPtr a, std::ptrdiff_t rowsize_in_bytes) -> typename type_from_x_iterator< planar_pixel_iterator< ChannelPtr, rgba_t > >::view_t  
```

---

## Comment 5

> Username: planetmarshall  
> Created at: 2025-12-08 21:29:14 UTC  
> Url: https://github.com/boostorg/gil/issues/781#issuecomment-3629080694  

Many thanks for that - I have something working now. I still think an example and a note in the docs would be useful as it wasn't immediately obvious to me how to do this - but I'm happy to contribute something.

---

## Comment 6

> Username: mloskot  
> Created at: 2025-12-08 22:48:28 UTC  
> Url: https://github.com/boostorg/gil/issues/781#issuecomment-3629347419  

Please, @planetmarshall, feel encouraged to contribute to the docs.
