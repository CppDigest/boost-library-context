# #392 Implement Sobel and Scharr operators [Merged]

> Username: simmplecoder  
> Created at: 2019-09-24 18:43:22 UTC  
> Updated at: 2023-02-01 10:09:49 UTC  
> Merged at: 2019-10-29 16:38:05 UTC  
> Closed at: 2019-10-29 16:38:05 UTC  
> Url: https://github.com/boostorg/gil/pull/392  

### Description  
  
This commit adds Sobel and Scharr  
operators with support for 0th and 1st  
degrees with other degrees planned for  
later  
  
### References  
  
https://www.researchgate.net/publication/239398674_An_Isotropic_3_3_Image_Gradient_Operator  
  
https://www.researchgate.net/profile/Hanno_Scharr/publication/220955743_Optimal_Filters_for_Extended_Optical_Flow/links/004635151972eda98f000000/Optimal-Filters-for-Extended-Optical-Flow.pdf  
  
### Tasklist  
  
- [X] Implement the operators  
- [X] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-10-01 20:11:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/392#pullrequestreview-295857882  

📁 example/Jamfile

```diff
  24 |     histogram.cpp
  25 |     harris.cpp
  26 |+     sobel_scharr
```

> Username: mloskot  
> Created_at: 2019-10-01 20:02:15 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r330248305  

`.cpp` is missing


📁 include/boost/gil/detail/math.hpp

```diff
  23 |+ 
  24 |+ template <typename T, typename Allocator>
  25 |+ inline kernel_2d<T, Allocator> get_identity_kernel() {
```

> Username: mloskot  
> Created_at: 2019-10-01 20:03:45 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r330248924  

` {` should go in separate line


📁 include/boost/gil/image_processing/numeric.hpp

```diff
  14 | #include <boost/gil/typedefs.hpp>
  15 |+ #include <boost/gil/extension/numeric/kernel.hpp>
  16 |+ #include <boost/gil/extension/numeric/convolve.hpp>
```

> Username: mloskot  
> Created_at: 2019-10-01 20:05:09 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r330249483  

It's a good idea to sort includes within blocks, see https://github.com/boostorg/gil/wiki/Include-Directives-Order

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 136 |+ /// https://www.researchgate.net/publication/239398674_An_Isotropic_3_3_Image_Gradient_Operator
 137 |+ template <typename T = float, typename Allocator = std::allocator<T>>
 138 |+ inline kernel_2d<T, Allocator> generate_dx_sobel(unsigned int degree = 1)
```

> Username: mloskot  
> Created_at: 2019-10-01 20:08:45 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r330250939  

I started thinking if `numeric.hpp` bag should be split into multiple specialised headers then this would go to e.g. `sobel.hpp`  
  
However, such refactoring may be depending on the merging of numeric into core.   
@stefanseefeld what's the status of this plan? ->https://lists.boost.org/boost-gil/2019/09/0296.php

> Username: simmplecoder  
> Created_at: 2019-10-08 06:37:23 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r332353460  

I believe I will be doing that after full cleanup, like moving everything to kernel_2d, adding more tests, etc. If you don't mind that approach, could you please close the issue?

> Username: mloskot  
> Created_at: 2019-10-08 08:08:34 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r332382148  

...or `sobel.hpp`, `scharr.hpp`, ...  
  
Yes, sounds good. Resolving this part.


📁 test/core/image_processing/sobel_scharr.cpp

```diff
  14 |+         kernel.end(),
  15 |+         gil::dx_sobel.begin()
  16 |+     ));
```

> Username: mloskot  
> Created_at: 2019-10-01 20:10:40 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r330251694  

I think single line would be fine here, as [it can be up to 100 characters long](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines).


---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-10-01 20:13:10 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-537209023  

yes, yes, I still plan to refactor the code so the `numeric` extension will be fused into `core`. Sorry for my slowness... :-(

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-10-01 20:17:41 UTC  
> Updated_at: 2019-10-01 20:18:09 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-537210779  

@stefanseefeld   
> I still plan to refactor the code so the `numeric` extension will be fused into `core`  
  
Okay, so if @simmplecoder doesn't prefer to do it now, then we can split `numeric.hpp` into multiple headers during or after the fuse.

---

## Comment 4

> Username: simmplecoder  
> Created_at: 2019-10-08 06:21:49 UTC  
> Updated_at: 2019-10-09 04:31:17 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-539357919  

@mloskot please remind me to re-enable failing tests before merge. I commented out the `compile-fail` targets, as those were failing. I wanted to find problems in my own code first. I found one, and am in the process of fixing it.

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-10-08 08:10:27 UTC  
> Updated_at: 2019-10-09 04:31:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-539401468  

@simmplecoder   
> @mloskot please remind me to re-enable failing tests before merge.   
  
AFAIS, this request is no longer valid as you've restored the tests.

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2019-10-08 08:11:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/392#pullrequestreview-298595758  

@simmplecoder LGTM.  
  
However, please ask and wait for @stefanseefeld 's review and consider his review superior to mine.

---

## Comment 7

> Username: simmplecoder  
> Created_at: 2019-10-22 16:37:21 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545049087  

@stefanseefeld , would you like me to unify the interface of all kernel generator functions in this PR?

---

## Comment 8

> Username: stefanseefeld  
> Created_at: 2019-10-22 17:38:37 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545073361  

@simmplecoder , sorry, I don't entirely understand the question. What do you mean by "unify" ?

---

## Comment 9

> Username: simmplecoder  
> Created_at: 2019-10-22 17:43:52 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545075514  

@stefanseefeld, earlier functions that generate Gaussian and mean kernels return `image_view`, while these return `kernel_2d`. Would you like me to make all functions return `kernel_2d`?

---

## Comment 10

> Username: stefanseefeld  
> Created_at: 2019-10-22 17:44:41 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545075874  

Ah, yes, I think that would be preferable. Thanks,

---

## Comment 11

> Username: mloskot  
> Created_at: 2019-10-22 17:57:51 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545081235  

@stefanseefeld  
> Ah, yes, I think that would be preferable.  
  
I agree that it's better to (create and) use dedicated types for job than trying to (bend to) re-use existing types where they don't necessarily fit, neither conceptually nor physically.  
Another aspect is that if we use types like `image_view` as general-purpose types we are somewhat tightening the coupling between functions operating on kernels and `image_view` preventing treating those as completely independent entities. This may cause problems in future (e.g. it may be hard to change either).

---

## Comment 12

> Username: simmplecoder  
> Created_at: 2019-10-23 06:16:41 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545286012  

@miralshah365 , could you please check if I broke your code? I migrated small part of your code that relied on old `image_view` interface for kernel generators.

---

## Comment 13

> Username: simmplecoder  
> Created_at: 2019-10-23 07:24:53 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545307932  

The output of Harris and Hessian seem to be differ from before the migration to `kernel_2d`. I will investigate that further, but I believe the interface should be more or less stable now. Hopefully I will be able to find what's wrong until the deadline.

---

## Comment 14

> Username: mloskot  
> Created_at: 2019-10-23 09:55:14 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545365821  

@simmplecoder If there is anything to update in @miralshah365 's code and she's offline, then don't hesitate to nudge me. I will try to take care of updating Miral's code.  
  
AFAIS, current failures are in the kernel tests, see https://dev.azure.com/boostorg/gil/_build/results?buildId=701&view=logs&jobId=2517ed61-6924-508d-087f-7c02f775cbba  
  
  
```  
 "D:\a\1\s\boost-root\libs\gil\_build\test\core\image_processing\test_core_image_processing_simple_kernels.vcxproj" (default target) (26) ->  
       (ClCompile target) ->   
         d:\a\1\s\boost-root\libs\gil\test\core\image_processing\simple_kernels.cpp(20): error C2664: 'boost::gil::kernel_2d<float,std::allocator<T>> boost::gil::generate_normalized_mean<float,std::allocator<T>>(size_t)': cannot convert argument 1 from 'boost::gil::image_view<boost::gil::gray32f_loc_t>' to 'size_t' [D:\a\1\s\boost-root\libs\gil\_build\test\core\image_processing\test_core_image_processing_simple_kernels.vcxproj]  
         d:\a\1\s\boost-root\libs\gil\test\core\image_processing\simple_kernels.cpp(39): error C2664: 'boost::gil::kernel_2d<float,std::allocator<T>> boost::gil::generate_normalized_mean<float,std::allocator<T>>(size_t)': cannot convert argument 1 from 'boost::gil::image_view<boost::gil::gray32f_loc_t>' to 'size_t' [D:\a\1\s\boost-root\libs\gil\_build\test\core\image_processing\test_core_image_processing_simple_kernels.vcxproj]  
         d:\a\1\s\boost-root\libs\gil\test\core\image_processing\simple_kernels.cpp(51): error C2664: 'boost::gil::kernel_2d<float,std::allocator<T>> boost::gil::generate_unnormalized_mean<float,std::allocator<T>>(size_t)': cannot convert argument 1 from 'boost::gil::image_view<boost::gil::gray32f_loc_t>' to 'size_t' [D:\a\1\s\boost-root\libs\gil\_build\test\core\image_processing\test_core_image_processing_simple_kernels.vcxproj]  
         d:\a\1\s\boost-root\libs\gil\test\core\image_processing\simple_kernels.cpp(69): error C2664: 'boost::gil::kernel_2d<float,std::allocator<T>> boost::gil::generate_unnormalized_mean<float,std::allocator<T>>(size_t)': cannot convert argument 1 from 'boost::gil::image_view<boost::gil::gray32f_loc_t>' to 'size_t' [D:\a\1\s\boost-root\libs\gil\_build\test\core\image_processing\test_core_image_processing_simple_kernels.vcxproj]  
         d:\a\1\s\boost-root\libs\gil\test\core\image_processing\simple_kernels.cpp(81): error C2664: 'boost::gil::kernel_2d<float,std::allocator<T>> boost::gil::generate_gaussian_kernel<float,std::allocator<T>>(size_t,double)': cannot convert argument 1 from 'boost::gil::image_view<boost::gil::gray32f_loc_t>' to 'size_t' [D:\a\1\s\boost-root\libs\gil\_build\test\core\image_processing\test_core_image_processing_simple_kernels.vcxproj]  
         d:\a\1\s\boost-root\libs\gil\test\core\image_processing\simple_kernels.cpp(114): error C2664: 'boost::gil::kernel_2d<float,std::allocator<T>> boost::gil::generate_gaussian_kernel<float,std::allocator<T>>(size_t,double)': cannot convert argument 1 from 'boost::gil::image_view<boost::gil::gray32f_loc_t>' to 'size_t' [D:\a\1\s\boost-root\libs\gil\_build\test\core\image_processing\test_core_image_processing_simple_kernels.vcxproj]  
```

---

## Comment 15

> Username: simmplecoder  
> Created_at: 2019-10-23 15:35:12 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545503409  

@mloskot, I made a lot of changes after your approval, could you please have a look at new changes?

---

## Comment 16

> Username: simmplecoder  
> Created_at: 2019-10-23 15:56:34 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545513337  

FYI, I checked the output of the Harris and Hessian with `cmp --silent $old $new || echo "files are different"` of commits 5abd20ecfc2ad4bb3c1306d6c7c20cdb5f336ea7 (needs fixing cmake due to my bad rebase) and 42a3ea0f00516b1d83c319cb7787fe3bd6ab1f32, the files are identical

---

## Comment 17

> Username: simmplecoder  
> Created_at: 2019-10-23 15:58:27 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-545514335  

@stefanseefeld , I believe I did all the changes I wanted. The PR is ready for review.

---

## Review 18 [Approved]

> Username: mloskot  
> Created_at: 2019-10-23 19:36:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/392#pullrequestreview-306120933  

LGTM. Thank you!

📁 include/boost/gil/image_processing/harris.hpp

```diff
  46 | 
  45 |-     auto const window_length = weights.dimensions().x;
  47 |+     auto const window_length = weights.size();
```

> Username: mloskot  
> Created_at: 2019-10-23 19:01:53 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338225274  

Minor naming remark: wouldn't `width` or `x_size` fit better as name here than `length`?


📁 include/boost/gil/image_processing/hessian.hpp

```diff
  23 |     GradientView ddyy,
  23 |-     Weights weights,
  24 |+     const kernel_2d<T, Allocator>& weights,
```

> Username: mloskot  
> Created_at: 2019-10-23 19:06:34 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338227199  

Minor stylistic remark: since in C++ this reads from right to left as _"`weights` is a reference **to**  const `kernel_2d` object"_, as we do it for pointers, this should be the [East Const](http://slashslash.info/eastconst/) way: `kernel_2d<T, Allocator> const& weights`  
  
You may want to join the revolution! 😉   
  
![image](https://user-images.githubusercontent.com/80741/67425761-f396d400-f5d8-11e9-81e9-20d7910f7c52.png)


📁 include/boost/gil/image_processing/numeric.hpp

```diff
  15 |+ #include <boost/gil/detail/math.hpp>
  16 |+ // fixes ambigious call to std::abs, https://stackoverflow.com/a/30084734/4593721
  17 |+ #include <cstdlib>
```

> Username: mloskot  
> Created_at: 2019-10-23 19:24:13 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338234519  

Not a request for change, just a remark: it would be better and clearer for future readers to do  
  
```cpp  
#if (defined(BOOST_CLANG) || defined(BOOST_GCC)) && !defined(BOOST_GNU_STDLIB)  
#include <cstdlib>  
#endif   
```  
  
but I'm not entirely sure that is the best way to detect libc++.  
  
Apparently, there seem to be no `BOOST_GNU_STDLIB` counterpart for libc++ like `BOOST_LIBCXX_STDLIB`. I have been asked about it here https://cpplang.slack.com/archives/C27KZLB0X/p1571857778008400

> Username: mloskot  
> Created_at: 2019-10-24 07:41:28 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338423753  

@simmplecoder Let's scratch that idea. In order to detect libc++ we need to include a header from it, so you might as well just include `cstdlib` header.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 155 |- inline void generate_normalized_mean(boost::gil::gray32f_view_t dst)
  81 |+ template <typename T = float, typename Allocator = std::allocator<T>>
  82 |+ inline kernel_2d<T, Allocator> generate_normalized_mean(std::size_t side_length)
```

> Username: mloskot  
> Created_at: 2019-10-23 19:25:06 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338234886  

Again, wouldn't `width` or `x_size` fit better as name here than `length`?

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  76 | /// \ingroup ImageProcessingMath
  77 | ///
  78 | /// Fills supplied view with normalized mean
```

> Username: mloskot  
> Created_at: 2019-10-23 19:28:26 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338236303  

Perhaps you could run find and replace for `/// Fills supplied view` changing to `/// Fills kernel`. This could be done even after merge as single commit with `[ci skip]` tag in subject line. No need to CI this kind of change.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 162 |+         {
 163 |+             kernel_2d<T, Allocator> result(3, 1, 1);
 164 |+             std::copy(dx_sobel.begin(), dx_sobel.end(), result.begin());
```

> Username: mloskot  
> Created_at: 2019-10-23 19:30:52 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338237322  

Since we have compile-time known `std::array<float, 9> dx_sobel`, this place seems like a good opportunity to add `static_assert` verifying `dx_sobel.size()` and `BOOST_ASSERT` verifying `dx_sobel.size() == result.size()`.  
  
Such assertions would also serve as code comment for a reader who doesn't have to jump to `dx_sobel` definition.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 192 |+         {
 193 |+             kernel_2d<T, Allocator> result(3, 1, 1);
 194 |+             std::copy(dx_scharr.begin(), dx_scharr.end(), result.begin());
```

> Username: mloskot  
> Created_at: 2019-10-23 19:33:30 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338238386  

Similarly, I'd add some assertions.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 222 |+         {
 223 |+             kernel_2d<T, Allocator> result(3, 1, 1);
 224 |+             std::copy(dy_sobel.begin(), dy_sobel.end(), result.begin());
```

> Username: mloskot  
> Created_at: 2019-10-23 19:33:50 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338238524  

And assertions here.

---

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 252 |+         {
 253 |+             kernel_2d<T, Allocator> result(3, 1, 1);
 254 |+             std::copy(dy_scharr.begin(), dy_scharr.end(), result.begin());
```

> Username: mloskot  
> Created_at: 2019-10-23 19:34:03 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338238613  

And assertions here too 😄


📁 include/boost/gil/image_processing/threshold.hpp

```diff
 435 |-         );
 436 |- 
 423 |+         kernel_2d<float> kernel = generate_gaussian_kernel(kernel_size, 1.0);
```

> Username: mloskot  
> Created_at: 2019-10-23 19:35:32 UTC  
> Updated_at: 2019-10-29 14:09:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#discussion_r338239246  

` kernel_2d<float> const kernel`?  
  
This simple addition immediately clears potential reader's question "can a thing be modified by function call that follows?".


---

## Comment 19

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-06 15:53:17 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814232447  

@simmplecoder  I was using the sobel operator for gradient along x direction for the photo attached below. The sobel operator is of degree 1. To my surprise, after applying the sobel operator, the pixel values are coming of the magnitude of 65000 in the gradient image. Is it possible? I used the sobel_scharr.cpp file in the example folder of boost gil and simply passed the name of the operator and the name of the picture and the other arguments as mentioned. I tested the same picture by applying sobel in opencv in python and found the pixel values to be as it should be. If you can kindly help me regarding this issue,I will be highly grateful  
![cat](https://user-images.githubusercontent.com/47924148/113739899-df3b8e00-971d-11eb-9628-6d23997edf3d.png)

---

## Comment 20

> Username: simmplecoder  
> Created_at: 2021-04-06 17:08:58 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814285833  

@Sayan-Chaudhuri perhaps you are using unsigned images and views? IIRC the read functions cannot read signed images, but you can convert them into signed or use `color_converted_view`. The destination view must be signed as well.

---

## Comment 21

> Username: simmplecoder  
> Created_at: 2021-04-06 18:31:32 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814347919  

I will look into it tomorrow and let you know. Perhaps I forgot to update  
the example or something

---

## Comment 22

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-06 19:15:08 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814374939  

Thanks for taking out time to look into the matter

---

## Comment 23

> Username: meshtag  
> Created_at: 2021-04-07 02:56:38 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814563993  

@simmplecoder  @Sayan-Chaudhuri , I believe the issue was caused due to ```16```  in   
 ```gil::gray16_image_t dx_image(input_image.dimensions());``` and ```gil::gray16_image_t dy_image(input_image.dimensions());```.  
Changing them to ```gil::gray8_image_t dx_image(input_image.dimensions())``` and ```gil::gray8_image_t dy_image(input_image.dimensions())``` solved the problem.

---

## Comment 24

> Username: meshtag  
> Created_at: 2021-04-07 02:59:38 UTC  
> Updated_at: 2021-04-07 03:10:58 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814564901  

@simmplecoder , perhaps we can add a check inside ```convolve_2d()``` for handling such things? Is it a good idea to use ```channel_traits``` for this purpose?  (/cc @mloskot @lpranam )

---

## Comment 25

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-07 03:00:46 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814565248  

Yes, I also found that but why did this happen?

---

## Comment 26

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-07 03:01:01 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814565323  

@meshtag

---

## Comment 27

> Username: meshtag  
> Created_at: 2021-04-07 03:04:39 UTC  
> Updated_at: 2021-04-07 03:05:51 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814566366  

Without doing an in depth analysis, my initial thoughts suggest that we are probably looking at some kind of an overflow inside ```convolve_2d()```. I will have to test it more in order to reach to a conclusive reason.

---

## Comment 28

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-07 03:30:37 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814574413  

Should I make a pull request if i find out the reason and solve it?@meshtag

---

## Comment 29

> Username: meshtag  
> Created_at: 2021-04-07 03:45:45 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814578612  

I am waiting for feedback on [this](https://github.com/boostorg/gil/pull/392#issuecomment-814564901). If we all agree on the root cause of this problem and my suggestion to solve it, I would like to create a PR myself. Lets wait until we hear from more experienced developers.

---

## Comment 30

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-07 04:38:06 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814593844  

ok.

---

## Comment 31

> Username: simmplecoder  
> Created_at: 2021-04-07 04:49:30 UTC  
> Updated_at: 2021-04-07 04:50:02 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814597392  

@meshtag I believe @Scramjet911 didn't have much luck with channel traits. It seems like whatever it returns is not what we need. The problem with the example is that it uses unsigned type where negative values will be present. The fix to use `gray8_image_t` is wrong, because it uses unsigned type too. I will PR the fix and mention everybody in it this evening.

---

## Comment 32

> Username: Scramjet911  
> Created_at: 2021-04-07 05:04:10 UTC  
> Updated_at: 2021-04-07 05:04:40 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-814602791  

> @meshtag I believe @Scramjet911 didn't have much luck with channel traits. It seems like whatever it returns is not what we need. The problem with the example is that it uses unsigned type where negative values will be present. The fix to use `gray8_image_t` is wrong, because it uses unsigned type too. I will PR the fix and mention everybody in it this evening.  
  
@simmplecoder Channel traits was working fine, but the problem was that the destination image of that case was of floating type, giving me the wrong result. So I had to use the source channel traits for checking the constraints.

---

## Comment 33

> Username: simmplecoder  
> Created_at: 2021-04-07 18:23:30 UTC  
> Updated_at: 2021-04-07 18:24:24 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-815126932  

@Sayan-Chaudhuri @meshtag unfortunately I didn't get time to write saturate cast, but you can tweak the equation inside `transform_pixels` below:  
  
```#include <boost/gil/typedefs.hpp>  
#include <boost/gil/image_processing/numeric.hpp>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/gil/extension/numeric/convolve.hpp>  
#include <string>  
#include <iostream>  
  
namespace gil = boost::gil;  
  
int main(int argc, char* argv[])  
{  
    if (argc != 5)  
    {  
        std::cerr << "usage: " << argv[0] << ": <input.png> <sobel|scharr> <output-x.png> <output-y.png>\n";  
        return -1;  
    }  
  
    gil::gray8_image_t input_image;  
    gil::read_image(argv[1], input_image, gil::png_tag{});  
    auto input = gil::view(input_image);  
    auto filter_type = std::string(argv[2]);  
  
    gil::gray16s_image_t dx_image(input_image.dimensions());  
    auto dx = gil::view(dx_image);  
    gil::gray16s_image_t dy_image(input_image.dimensions());  
    auto dy = gil::view(dy_image);  
  
    auto signed_input = gil::color_converted_view<gil::gray8s_pixel_t>(input);  
    if (filter_type == "sobel")  
    {  
        gil::detail::convolve_2d(signed_input, gil::generate_dx_sobel(1), dx);  
        gil::detail::convolve_2d(signed_input, gil::generate_dy_sobel(1), dy);  
    }  
    else if (filter_type == "scharr")  
    {  
        gil::detail::convolve_2d(signed_input, gil::generate_dx_scharr(1), dx);  
        gil::detail::convolve_2d(signed_input, gil::generate_dy_scharr(1), dy);  
    }  
    else  
    {  
        std::cerr << "unrecognized gradient filter type. Must be either sobel or scharr\n";  
        return -1;  
    }  
  
    gil::gray8_image_t output_image(input_image.dimensions());  
    auto output = gil::view(output_image);  
  
    gil::transform_pixels(dx, output, [](const gil::gray16s_pixel_t& dx_pixel) {  
        double value = dx_pixel[0];  
        return gil::gray8_pixel_t((value - std::numeric_limits<std::int16_t>::min()) / (std::numeric_limits<std::int16_t>::max()) * 255);  
    });  
  
    gil::write_view(argv[3], output, gil::png_tag{});  
  
    gil::transform_pixels(dy, output, [](const gil::gray16s_pixel_t& dx_pixel) {  
      double value = dx_pixel[0];  
      return gil::gray8_pixel_t((value - std::numeric_limits<std::int16_t>::min()) / (std::numeric_limits<std::int16_t>::max()) * 255);  
    });  
    gil::write_view(argv[4], output, gil::png_tag{});  
}  
```  
  
The main problem now is that the values are incorrectly scaled. I bet opencv uses `saturate_cast` for this, but I'm not sure. I will investigate tomorrow.

---

## Comment 34

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-08 03:08:04 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-815412039  

Thanks @simmplecoder  I shall look into it

---

## Comment 35

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-08 16:42:13 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-815975022  

@simmplecoder  I wanted to confirm that sobel and scharr currently dont work for multichannel images right?

---

## Comment 36

> Username: simmplecoder  
> Created_at: 2021-04-08 18:10:46 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-816032033  

You’re correct. You could use channel views to get it to work for multi  
channel images.  
  
On Thu, 8 Apr 2021 at 22:42 Sayan-Chaudhuri ***@***.***>  
wrote:  
  
> @simmplecoder <https://github.com/simmplecoder> I wanted to confirm that  
> sobel and scharr currently dont work for multichannel images right?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/gil/pull/392#issuecomment-815975022>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ADGZGQC2K6GISVJATSKLNATTHXMHTANCNFSM4I2DNP4Q>  
> .  
>

---

## Comment 37

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-09 14:16:21 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-816714933  

@simmplecoder  I think for proper scaling we should be using this formula   
NewValue = (((OldValue - OldMin) * (NewMax - NewMin)) / (OldMax - OldMin)) + NewMin right??  
In transform pixels function , I think you have used this formula but you have given the denominator wrong

---

## Comment 38

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-10 07:17:49 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-817092629  

@simmplecoder  Also,how does the convolution operator in boost deal with pixels at boundaries?

---

## Comment 39

> Username: cgringmuth  
> Created_at: 2023-02-01 09:04:13 UTC  
> Updated_at: 2023-02-01 10:09:49 UTC  
> Url: https://github.com/boostorg/gil/pull/392#issuecomment-1411698300  

> @Sayan-Chaudhuri @meshtag unfortunately I didn't get time to write saturate cast, but you can tweak the equation inside `transform_pixels` below:  
>   
> ```  
> #include <boost/gil/image_processing/numeric.hpp>  
> #include <boost/gil/extension/io/png.hpp>  
> #include <boost/gil/extension/numeric/convolve.hpp>  
> #include <string>  
> #include <iostream>  
>   
> namespace gil = boost::gil;  
>   
> int main(int argc, char* argv[])  
> {  
>     if (argc != 5)  
>     {  
>         std::cerr << "usage: " << argv[0] << ": <input.png> <sobel|scharr> <output-x.png> <output-y.png>\n";  
>         return -1;  
>     }  
>   
>     gil::gray8_image_t input_image;  
>     gil::read_image(argv[1], input_image, gil::png_tag{});  
>     auto input = gil::view(input_image);  
>     auto filter_type = std::string(argv[2]);  
>   
>     gil::gray16s_image_t dx_image(input_image.dimensions());  
>     auto dx = gil::view(dx_image);  
>     gil::gray16s_image_t dy_image(input_image.dimensions());  
>     auto dy = gil::view(dy_image);  
>   
>     auto signed_input = gil::color_converted_view<gil::gray8s_pixel_t>(input);  
>     if (filter_type == "sobel")  
>     {  
>         gil::detail::convolve_2d(signed_input, gil::generate_dx_sobel(1), dx);  
>         gil::detail::convolve_2d(signed_input, gil::generate_dy_sobel(1), dy);  
>     }  
>     else if (filter_type == "scharr")  
>     {  
>         gil::detail::convolve_2d(signed_input, gil::generate_dx_scharr(1), dx);  
>         gil::detail::convolve_2d(signed_input, gil::generate_dy_scharr(1), dy);  
>     }  
>     else  
>     {  
>         std::cerr << "unrecognized gradient filter type. Must be either sobel or scharr\n";  
>         return -1;  
>     }  
>   
>     gil::gray8_image_t output_image(input_image.dimensions());  
>     auto output = gil::view(output_image);  
>   
>     gil::transform_pixels(dx, output, [](const gil::gray16s_pixel_t& dx_pixel) {  
>         double value = dx_pixel[0];  
>         return gil::gray8_pixel_t((value - std::numeric_limits<std::int16_t>::min()) / (std::numeric_limits<std::int16_t>::max()) * 255);  
>     });  
>   
>     gil::write_view(argv[3], output, gil::png_tag{});  
>   
>     gil::transform_pixels(dy, output, [](const gil::gray16s_pixel_t& dx_pixel) {  
>       double value = dx_pixel[0];  
>       return gil::gray8_pixel_t((value - std::numeric_limits<std::int16_t>::min()) / (std::numeric_limits<std::int16_t>::max()) * 255);  
>     });  
>     gil::write_view(argv[4], output, gil::png_tag{});  
> }  
> ```  
>   
> The main problem now is that the values are incorrectly scaled. I bet opencv uses `saturate_cast` for this, but I'm not sure. I will investigate tomorrow.  
  
I think your scaling is not correct. If you move values such that min is 0 then you just cannot scale by max. Also I think it is better to scale based on the range of image instead of the range of pixel type [-32768, 32767]. Imagine your range of your gradient is [-10, 10]. You would barely see them if you subtract -32768 and divide by 32767 . But if you map -10 -> 0 and 10 -> 255 then you can see them clearly. So my idea was to stretch the range of your gradients to max range of output type.  
  
Here is mine version to write to png files  
  
```cpp  
template<typename InputView>  
void write_view(const InputView& img, const std::string& filename) {  
  gil::gray8_image_t output_image(img.dimensions());  
  auto output = gil::view(output_image);  
  
  const auto [minPix, maxPix] = std::minmax_element(img.begin(), img.end());  
  const auto minVal = minPix[0];  
  const auto maxVal = maxPix[0];  
  gil::transform_pixels(img, output, [&minVal, &maxVal](const auto& pix) {  
      double value = pix[0];  
      return gil::gray8_pixel_t((value - minVal) / (maxVal - minVal) * 255);  
  });  
  
  gil::write_view(filename, output, gil::png_tag{});  
}  
```  
Now you have nice gradients (this [image](https://wow.zamimg.com/uploads/blog/images/18668-warcraft-iii-reforged-patch-notes-1-32-3-campaign-art-gameplay-fixes.jpg) has been used):  
|dx | dy|  
|--- | --- |  
| <img src=https://user-images.githubusercontent.com/1169481/215995104-0249132f-95c8-4ddc-ba03-4f5566a57888.png width="400"> | <img src=https://user-images.githubusercontent.com/1169481/215995110-9989ec6b-c712-48c8-a322-8ee170f69a91.png width="400">|  
  
However, the dx and dy is still switched because of a bug. Please see this PR #723.

---
