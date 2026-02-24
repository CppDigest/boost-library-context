# #496 Add extension with basic support for Blaze library [Open]

> Username: simmplecoder  
> Created at: 2020-05-14 20:06:30 UTC  
> Updated at: 2020-10-31 19:57:19 UTC  
> Url: https://github.com/boostorg/gil/pull/496  

Add conversions image<->matrix and  
vector<->pixel. Bump CXX_STANDARD if  
Blaze is used. Minimal support for  
BLAS library selection  
  
### Description  
  
This PR adds integration with [Blaze](https://bitbucket.org/blaze-lib/blaze/src/master/) linear algebra library. The main motivation behind it is that some image processing algorithms make heavy use of linear algebra operations, having image(s) only in the input and output.  
  
Algorithms like Harris corner detector can be implemented in under 15 lines (given a convolution function and kernels):  
```cpp  
blaze::DynamicMatrix<std::int64_t>  
harris(const blaze::DynamicMatrix<std::int64_t>& image, double k)  
{  
    auto dx = flash::convolve(image, flash::sobel_x);  
    auto dy = flash::convolve(image, flash::sobel_y);  
  
    auto dx_2 = dx % dx;  
    auto dy_2 = dy % dy;  
  
    auto dxdy = flash::convolve(dx, flash::sobel_y);  
  
    auto ktrace_2 = (dx_2 + dy_2) % (dx_2 + dy_2) * k;  
    auto det = dx_2 % dy_2 - dxdy % dxdy;  
    return det - ktrace_2;  
}  
```  
The only thing that is not clear is probably `operator%`. It is Schur product, e.g. elementwise multiplication.  
  
Aside from convenience, properly configured Blaze offers great performance.  
  
### References  
  
https://bitbucket.org/blaze-lib/blaze/src/master/  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [X] Add functions into `extension/blaze/blaze.hpp`  
- [X] Add minimal example  
- [ ] Improve CMake scripts to support more than default and MKL  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: simmplecoder  
> Created_at: 2020-05-14 20:41:06 UTC  
> Updated_at: 2020-05-14 20:42:33 UTC  
> Url: https://github.com/boostorg/gil/pull/496#issuecomment-628875619  

For anybody who would like to review this extension, I have some questions you might want to consider:  
  
1. `to_matrix` has capability to operate only on single channel images and returns `CustomMatrix<ChannelType>`, which is like `ChannelType*`, e.g. copy is shallow. Do you think it is a good idea?  
  
2. Channeled conversion (`to_matrix_channeled`), returns a copy in a `DynamicMatrix`. Do you think there are enough use cases to support other matrix types or out parameters?  
  
3. Do you think returning `CustomMatrix` on multi-channel images is worth it? It will require checking layout compatibility of `StaticVector<ChannelType, num_channels>` with pixel type, which might not work all the time.  
  
4. In case you tried it out, how easy was it to get it up and running?

---

## Comment 2

> Username: simmplecoder  
> Created_at: 2020-05-15 20:42:20 UTC  
> Url: https://github.com/boostorg/gil/pull/496#issuecomment-629474444  

After thinking about it, I believe I figured out a few main usage axes.  
  
## Container related usages:  
  
1. Make Blaze think GIL's images are matrices. That is supported through `CustomMatrix`, and the name will probably be `as_matrix` to reflect it's cast-like nature.  
  
2. Copy into `DynamicMatrix`. The name will be `to_matrix`.  
  
## Channel related usages.   
  
I believe doing multi-channel IP/CV is hard because not all operations used in the fields that can be applied on say `double` are applicable on `blaze::StaticVector` without handrolling some functionality.  
  
1. Make scalar version that will only work on single channeled images, e.g. `gray8_image_t` or `gray16_image_t`. The name will suffix previous names with `_scalar`.  
  
2. Make multi-channel conversion that will work with homogeneous pixels, e.g. `rgb8_image_t`. The name will suffix previous names with `_channeled`. Do note that when combined with `as_matrix`, this might lead to undefined behavior if resulting Blaze vector type and GIL's pixel are layout incompatible. This is more of a power tool, and the main usage cases are to use it as read-only parameter into multiple (probably multi-threaded) functions.  
  
## Converting matrix into image  
  
Since type deduction can solve the overload resolution problem, only one function will be provided. The name could be changed, because `to_image` in context of GIL is a bit ambiguous, thus probably something like `from_matrix` will be used.   
  
1. If the element type of a Matrix is scalar and the specified resulting image type is single channel, then conversion will work, otherwise compilation error/UB.  
  
2. If the matrix element type is vector, then as long as length of the vector is equal or greater than channel count in resulting image, the conversion will work, otherwise compilation error/UB.  
  
## Returning an image and writing into view  
  
Since move semantics are added for common use cases, I believe just creating and returning a new image should not hurt performance in measurable way. There will also be an overload that will have an out parameter with view type, while the other will just return image.  
  
1. One overload for matrix to image function that will return the image.  
  
2. The other overload will have an out parameter with view type.

---
