# #443 Adding Gaussian Smoothening for images [Closed]

> Username: laxsuryavanshi  
> Created at: 2020-03-07 11:59:47 UTC  
> Updated at: 2020-03-08 10:02:12 UTC  
> Closed at: 2020-03-08 10:02:12 UTC  
> Url: https://github.com/boostorg/gil/pull/443  

**Description**  
Gaussian smoothening is the result of blurring an image with a Gaussian function.  
The implementation here takes advantage of the separable property of the Gaussian filter by dividing the process into two passes.  
  
**Motivation**  
This PR is proposed as a solution to the competency test for GSoC 2020. I chose the project proposed in the ideas list "Image Processing Algorithms". If accepted, I could start implementing more algorithms like Wiener filter, Median filter, Histogram (even though proposed separately)  
  
**Reference**  
https://en.wikipedia.org/wiki/Gaussian_filter  
http://rastergrid.com/blog/2010/09/efficient-gaussian-blur-with-linear-sampling/

---

## Review 1 [Changes requested]

> Username: lpranam  
> Created_at: 2020-03-07 19:32:33 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/443#pullrequestreview-370783868  

Please make the required changes further review can only be done after changes are submitted. Thank you for your contribution.

📁 include/boost/gil/image_processing/filter.hpp

```diff
 141 |+ 
 142 |+ template<typename KernelT>
 143 |+ void getGaussianKernel(KernelT& kernel,
```

> Username: lpranam  
> Created_at: 2020-03-07 19:16:37 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389305321  

You may wanna have a look at the already implemented gaussian kernel generator at:  
https://github.com/boostorg/gil/blob/f33128e20edd676728f826107adac19c22be9651/include/boost/gil/image_processing/numeric.hpp#L129   
  
Please consider using it. This may induce significant change in the code

> Username: laxsuryavanshi  
> Created_at: 2020-03-07 20:59:31 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389311782  

Yeah, I had a look over it. But it returns a 2D kernel. But Gaussian Kernel has a separable property hence 2D convolution is broken down into 2 passes of 1D convolution. Plz, correct me if I'm wrong:)

> Username: mloskot  
> Created_at: 2020-03-07 21:10:39 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389312401  

@laxsuryavanshi Please, follow naming used in the existing code. Any form of CamelCase for functions and classes is no-go, i.e. `getGaussianKernel` must read `get_gaussian_kernel`.  
  
> But Gaussian Kernel has a separable property hence 2D  
  
We could have utility converting our kernels 1D<->2D

> Username: laxsuryavanshi  
> Created_at: 2020-03-07 22:08:13 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389315727  

> We could have utility converting our kernels 1D<->2D  
  
This is a nice idea, will work on it

> Username: mloskot  
> Created_at: 2020-03-07 22:34:54 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389317206  

> > We could have utility converting our kernels 1D<->2D  
>   
> This is a nice idea, will work on it  
  
@laxsuryavanshi I forgot to mention, if you aim to add such conversion functions, please submit it as a separate PR (including at least minimal tests). Thanks!


📁 test/core/image_processing/gaussian_filter.cpp

```diff
  43 |+     gil::gray8_view_t dst_view(temp_view);
  44 |+ 
  45 |+     gil::gaussian_filter(src_view, dst_view, 3, 1.0f);
```

> Username: lpranam  
> Created_at: 2020-03-07 19:28:18 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389305988  

In case of your this implementation, even Gaussian kernel generation requires additional test but as I have suggested the changes above it would not be required.

> Username: laxsuryavanshi  
> Created_at: 2020-03-07 21:13:04 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389312534  

I verified output of the Gaussian Kernel Generation code with cv's getGaussianKernel function and I find it very much same up to some extent (precision after 4 decimals)


---

## Comment 2

> Username: mloskot  
> Created_at: 2020-03-07 20:17:59 UTC  
> Url: https://github.com/boostorg/gil/pull/443#issuecomment-596133766  

@laxsuryavanshi First, I'd like you to read two comments, https://github.com/boostorg/gil/pull/435#issuecomment-588371700 and https://github.com/boostorg/gil/pull/430#issuecomment-581576130, to learn more about the process of GSoC preparation and what to expect about your PR submitted as the competency test. You should also have a look at https://www.boost.org/community/gsoc.html

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-07 21:28:28 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/443#pullrequestreview-370789053  

📁 example/gaussian_blur.cpp

```diff
   1 |+ #include <boost/gil/extension/io/jpeg.hpp>
```

> Username: mloskot  
> Created_at: 2020-03-07 21:07:23 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389312222  

Licence and copyright is missing  
  
As I [mentioned on Slack](https://cpplang.slack.com/archives/CSVT0STV2/p1583593764075500), please try to apply the initial clean-up according to the GIL guidelines as well as Boost general guidelines, links on right hand side here https://www.boost.org/development/index.html


📁 include/boost/gil/image_processing/filter.hpp

```diff
  21 |+ #include <utility>
  22 |+ #include <algorithm>
  23 |+ #include <cmath>
```

> Username: mloskot  
> Created_at: 2020-03-07 21:08:27 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389312296  

[order alphabetically within each group](https://github.com/boostorg/gil/wiki/Include-Directives-Order)

> Username: laxsuryavanshi  
> Created_at: 2020-03-07 21:45:25 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389314439  

I'll update

---

📁 include/boost/gil/image_processing/filter.hpp

```diff
 146 |+                        bool normalize = true)
 147 |+ {
 148 |+     if (!(kernel_size & 0x1))
```

> Username: mloskot  
> Created_at: 2020-03-07 21:21:56 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389313032  

Some may read it as fancy syntax. The old school `size % 2 != 0` will generate the very same assembly :)

---

📁 include/boost/gil/image_processing/filter.hpp

```diff
 176 |+                      bool normalize = true,
 177 |+                      boundary_option option = boundary_option::extend_zero)
 178 |+ {
```

> Username: mloskot  
> Created_at: 2020-03-07 21:26:41 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389313310  

Nitpick, try to align the code to the left more than to the right  
  
```cpp  
template <typename SrcView, typename DstView>  
void gaussian_filter(  
    SrcView src_view,  
    DstView dst_view,  
    long int kernel_size,  
    double sigma,  
    bool normalize = true,  
    boundary_option option = boundary_option::extend_zero)  
{  
```


📁 include/boost/gil/image_processing/threshold.hpp

```diff
  47 | 
  48 |-     //iterate over the image chaecking each pixel value for the threshold
  48 |+     //iterate over the image checking each pixel value for the threshold
```

> Username: mloskot  
> Created_at: 2020-03-07 21:27:25 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389313350  

Good catch! Could you submit this as a separate bugfix PR?

> Username: laxsuryavanshi  
> Created_at: 2020-03-07 21:32:59 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389313715  

Yeah sure, will do

---

📁 include/boost/gil/image_processing/threshold.hpp

```diff
  66 | /// \brief Direction of image segmentation.
  67 |- /// The direction specifieds which pixels are considered as corresponding to object
  67 |+ /// The direction specifies which pixels are considered as corresponding to object
```

> Username: mloskot  
> Created_at: 2020-03-07 21:27:48 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389313368  

Could you also submit this as part of the bugfix PR?


📁 test/core/image_processing/gaussian_filter.cpp

```diff
   1 |+ #define BOOST_TEST_MODULE gil/test/core/image_processing/gaussian_filter
```

> Username: mloskot  
> Created_at: 2020-03-07 21:28:14 UTC  
> Updated_at: 2020-03-08 09:55:39 UTC  
> Url: https://github.com/boostorg/gil/pull/443#discussion_r389313449  

Licence and copyright notice is missing


---
