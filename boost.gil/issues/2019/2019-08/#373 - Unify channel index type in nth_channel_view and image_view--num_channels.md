# #373 - Unify channel index type in nth_channel_view and image_view::num_channels [Open]

> Username: mloskot  
> Created at: 2019-08-08 16:23:25 UTC  
> Updated at: 2022-05-17 19:56:34 UTC  
> Url: https://github.com/boostorg/gil/issues/373  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
- `image_view::num_channels()` returns `size_t`  
- `nth_channel_view(View, int n)` expects `int` for index  
  
Apart from signed and unsigned mix-up causing the annoying compilation warnings,  
if no rationale of the mix is provided, it's a sloppy design of the interface.  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
Use of common type for indexing of `image_view` channels.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
int main  
{  
    gil::rgb8_image_t img;  
    auto v = gil::view(img);  
    for (std::size_t i = 0; i < v.num_channels(); i++)  
        auto _ = nth_channel_view(v, i);  
}  
```

---

## Comment 1

> Username: sdebionne  
> Created at: 2022-05-04 19:21:21 UTC  
> Url: https://github.com/boostorg/gil/issues/373#issuecomment-1117728003  

A rationale could be that `int` is used almost [everywhere](https://github.com/boostorg/gil/blob/develop/include/boost/gil/color_base_algorithm.hpp) to identify the channel. I think it helps writing compile-time recursion for per-channel operation but if it' not the case we might want to extend "unification" to color base.

---

## Comment 2

> Username: mloskot  
> Created at: 2022-05-04 21:09:38 UTC  
> Url: https://github.com/boostorg/gil/issues/373#issuecomment-1117941296  

It has been a bit of a mess.  
If we think of the image in terms of 3D: X, Y and Z, then currently we define X and Y as `ptrdiff_t` and the X as `int` or `size_t`.  
  
I'm recalling https://github.com/boostorg/gil/pull/305 where some of us, @stefanseefeld, were preferring `size_t` to express the image-in-memory dimensions. I've been cultivating a more conservative approach to stick to `ptrdiff_t` as it is what the original authors of GIL (folks at Adobe) decided to use. Looks like it may be a good to review the current practice and decide on the `ptrdiff_t` vs `size_t` once for all :)  
  
My suggestion is to  
- use `ptrdiff_t` for sizes of abstract, virtual and physical sizes of image/band dimensions  
- use `size_t` for sizes in memory  
- if image dimension is used as size in memory, then `static_cast<size_t>` it, and vice versa.  
  
Please, have your say @sdebionne @striezel @stefanseefeld @lpranam  @chhenning @simmplecoder ...

---

## Comment 3

> Username: striezel  
> Created at: 2022-05-05 11:20:57 UTC  
> Url: https://github.com/boostorg/gil/issues/373#issuecomment-1118437569  

> A rationale could be that int is used almost [everywhere](https://github.com/boostorg/gil/blob/develop/include/boost/gil/color_base_algorithm.hpp) to identify the channel.  
  
Everywhere - except where it is not used. While `nth_channel_view` still uses `int` (unless #659 is approved, which would change that to `std::size_t`), many places that are using `nth_channel_view` are using `std::size_t` as channel index already. For example:  
* [image_processing/convolve.hpp](https://github.com/boostorg/gil/blob/36a45e3af566950acc50b6ab052f3e7d7b1fc0b7/include/boost/gil/image_processing/convolve.hpp#L433)  
* [image_processing/histogram_matching.hpp](https://github.com/boostorg/gil/blob/36a45e3af566950acc50b6ab052f3e7d7b1fc0b7/include/boost/gil/image_processing/histogram_matching.hpp#L173)  
* [image_processing/morphology.hpp](https://github.com/boostorg/gil/blob/36a45e3af566950acc50b6ab052f3e7d7b1fc0b7/include/boost/gil/image_processing/morphology.hpp#L118)  
* [image_processing/threshold.hpp](https://github.com/boostorg/gil/blob/36a45e3af566950acc50b6ab052f3e7d7b1fc0b7/include/boost/gil/image_processing/threshold.hpp#L340)  
  
Seems to me like most of the image processing stuff agreed that channels should be a `std::size_t`.  
  
> It has been a bit of a mess.  
  
Yeah, seems like it, but we can fix that - one PR at a time.  
   
> I'm recalling #305 where some of us, @stefanseefeld, were preferring `size_t` to express the image-in-memory dimensions. I've been cultivating a more conservative approach to stick to `ptrdiff_t` as it is what the original authors of GIL (folks at Adobe) decided to use. Looks like it may be a good to review the current practice and decide on the `ptrdiff_t` vs `size_t` once for all :)  
  
Basically I am with Stefan Seefeld on this one. `std::ptrdiff_t` allows for negative values, but there is no negative channel index. That's why I think `std::size_t` (or any unsigned type, for that matter) is the better choice here, because it conveys the notion that channel indices start at zero and cannot be negative. As a side effect, any compiler warning about signed / unsigned mixup with regards to channels would then be an indication for potential trouble. After all, passing a possibly negative `int` or `std::ptrdiff_t` as channel index is most likely not what people want to do anyway.

---

## Comment 4

> Username: sdebionne  
> Created at: 2022-05-05 17:05:44 UTC  
> Url: https://github.com/boostorg/gil/issues/373#issuecomment-1118842042  

> Everywhere - except where it is not used.  
  
Sorry, I meant "everywhere" in core. Image processing is a recent addition to the library (that I dont know much about TBH).  
  
I think there is a consensus that unsigned integers are error prone and that [using unsigned type for `size_t` was a mistake]( https://www.youtube.com/watch?v=4afySnY-XgY).  
  
OpenMP does not allow a for loop index to be `unsigned` (until recent iteration of the standard).

---

## Comment 5

> Username: striezel  
> Created at: 2022-05-06 21:34:53 UTC  
> Url: https://github.com/boostorg/gil/issues/373#issuecomment-1120024187  

I was not really expecting that people do arithmetic operations on the number of channels, but if that is a concern, then some signed type may indeed be better. In that case it should probably be `ptrdiff_t` instead, as was suggested earlier (https://github.com/boostorg/gil/issues/373#issuecomment-1118842042). I just do not fancy the name `ptrdiff_t` for a type that is used to represent the number of channels. The name `ptrdiff_t` may invoke the idea that this is dealing with pointers, which it is not. So maybe there should be a name like `boost::gil::channel_index_t` or `boost::gil::index_t` that then is aliased to `ptrdiff_t`.

---

## Comment 6

> Username: mloskot  
> Created at: 2022-05-17 19:56:34 UTC  
> Url: https://github.com/boostorg/gil/issues/373#issuecomment-1129261400  

@striezel   
> maybe there should be a name like `boost::gil::channel_index_t` or `boost::gil::index_t` that then is aliased to `ptrdiff_t`  
  
I don't like the `ptrdiff_t` name particularly.  
I don't say the name is clear across all contexts in GIL.  
I don't feel comfortable with `s/ptrdiff_t/size_t/g` across GIL as such seemingly trivial change may turn not as benign as it seems.  
I assume [Lubomir Bourdev](http://www.lubomir.org/) and Hailin Jin had reasons to choose `ptrdiff_t` which, unfortunately, remain undocumented.  
  
I do like the idea of readable and self-descriptive names, so I like the idea of aliasing `ptrdiff_t` as `boost::gil::index_t` and use it in all indexing situations in GIL (channel, pixel, x, y, etc.), and fix any existing mismatch to match `index_t`.  
Worth it to you too @sdebionne ?
