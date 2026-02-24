# #622 - Matrix like type for GIL [Open]

> Username: simmplecoder  
> Created at: 2021-07-31 19:17:15 UTC  
> Updated at: 2021-08-14 17:36:30 UTC  
> Url: https://github.com/boostorg/gil/issues/622  

### Is your feature request related to a problem? Please describe.  
  
Yes. Due to requests in #204 and #615 , it is clear that there is serious need for some sort of matrix like type that is not normalized by default. Image processing and related functionality is in a dire need of this.  
  
### Describe the solution you'd like  
  
#### C++ Example  
  
<!-- C++ program or snippet that shows preferred solution -->  
  
```cpp  
    // just 2D matrix of floats  
    using matrix32f = boost::gil::image<boost::gil::pixel<float, boost::gil::gray_layout_t>>;  
    using matrix32f_view = decltype(boost::gil::view(std::declval<matrix32f>()));  
  
    template <typename GrayPixel>  
    float saturate_cast(GrayPixel pixel);  
    template <typename GrayPixel>  
    GrayPixel saturate_cast(float matrix_cell);  
  
    // often needed to convert to/from images read from disk  
    template <typename GrayImageView>  
    void value_preserving_copy(GrayImageView source, matrix32f_view destination);  
  
    template <typename GrayImageView>  
    void saturate_cast_copy(matrix32f_view destination, GrayImageView source);  
```  
  
  
### Describe alternatives you've considered  
  
It is also possible to just implement the following:  
  
```cpp  
template <typename GrayImageView>  
void normalize_copy(GrayImageView source, gray32f_view_t destination);  
  
template <typename GrayImageView>  
void denormalize_copy(gray32f_view_t source, GrayImageView destination);  
```  
  
### Additional context  
  
I believe having just `matrix32f` will be enough for starters. Most of the CV and IP computations are done in floats anyway.

---

## Comment 1

> Username: sdebionne  
> Created at: 2021-08-01 15:36:55 UTC  
> Url: https://github.com/boostorg/gil/issues/622#issuecomment-890541108  

I also define my own typedefs for `float` and `double` when pixel are not exactly colors (photon counts in my case). But this is easily done in the app, no? I would not mind to have them predefined in GIL though. The default channel converter, that maps the range of the source channel type to the destination channel type, can also be customized, e.g.  a `round_channel_converter`.  Does that help?

---

## Comment 2

> Username: simmplecoder  
> Created at: 2021-08-14 17:36:30 UTC  
> Url: https://github.com/boostorg/gil/issues/622#issuecomment-898935045  

@sdebionne @lpranam @mloskot I believe we could mount a lot of functionality on properly written matrix type. Here is the first sketch:  
  
https://github.com/simmplecoder/gil/blob/2D_Convolution_and_Correlation/include/boost/gil/extension/numeric/arithmetics.hpp  
  
https://github.com/simmplecoder/gil/blob/2D_Convolution_and_Correlation/include/boost/gil/extension/numeric/matrix.hpp  
  
These are very rough sketches of what we need. Using those, I was able to fix Hessian corner detector's numeric problems in less than an hour. I believe it is definitely a needed feature. We will iterate on it over the years I guess. For now it supports only single channel float element, so the interface will probably be unstable.  
  
What I would like to do is to provide some simple building blocks on which we could keep building our image processing and other numeric algorithms. Something that would not cause a few pages of compilation errors, that would be reasonably fast or at least not terribly slow, something that is well understood numerically (signedness and range problems are nightmare, it is hard to say if it is bug in the algorithm or the input/output views are not properly typed) and remove the need for separate image/view variables (I guess we could add an implicit conversion function on image that would give the view, only for the matrix types). GIL has great abstractions, but it lacks useful defaults for the cases of image processing and computer vision.
