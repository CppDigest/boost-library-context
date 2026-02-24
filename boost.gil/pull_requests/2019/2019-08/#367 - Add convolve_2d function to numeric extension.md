# #367 Add convolve_2d function to numeric extension [Merged]

> Username: miralshah365  
> Created at: 2019-08-05 20:55:53 UTC  
> Updated at: 2019-10-23 21:38:52 UTC  
> Merged at: 2019-08-08 21:47:10 UTC  
> Closed at: 2019-08-08 21:47:10 UTC  
> Url: https://github.com/boostorg/gil/pull/367  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
`convolve` function renamed to `convolve_1d`  
  
for more detail check the reference(s)  
<!-- What does this pull request do? -->  
  
### References  
closes #356   
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-05 21:22:37 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-271012565  

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 276 | BOOST_FORCEINLINE
 277 |- void convolve(
 277 |+ void convolve_1d(
```

> Username: mloskot  
> Created_at: 2019-08-05 21:22:29 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r310797708  

@miralshah365 This is an interesting issue: ~~it~~ Does it break compatibility? so older clients will no longer be able to compile with future Boost.   
  
~~@stefanseefeld Would you have any comment on that, e.g. in case I'm overcautious.~~

> Username: mloskot  
> Created_at: 2019-08-05 21:30:46 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r310800438  

@miralshah365 I've just reminded myself the `convolve` function has been added very recently in #347 and not released yet. This change should not break any existing clients, other than those which use GIL from `develop` which is cutting edge, so breakage is possible.  
It's all good.  
  
p.s. It's good to be sensitive to this kind of changes though :-)

> Username: miralshah365  
> Created_at: 2019-08-05 21:42:00 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r310804096  

I never had such thought in my mind of breaking the compatibility for an existing client. I would now keep such thing while changing any existing code.


---

## Comment 2

> Username: mloskot  
> Created_at: 2019-08-06 11:01:36 UTC  
> Url: https://github.com/boostorg/gil/pull/367#issuecomment-518622018  

@miralshah365 Do you plan to work on more test cases?

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-07 12:00:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-271910906  

📁 test/extension/numeric/Jamfile

```diff
  26 | run matrix3x2.cpp ;
  27 | run numeric.cpp ;
  28 |+ convolve_2d.cpp ;
```

> Username: mloskot  
> Created_at: 2019-08-07 12:00:37 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311513687  

The `run` is missing and causing this failure at https://ci.appveyor.com/project/stefanseefeld/gil/builds/26526874/job/8lqgesnkn6bhs26c  
  
```  
libs\gil\test\extension\numeric\Jamfile:28: in modules.load  
ERROR: rule "convolve_2d.cpp" unknown in module "Jamfile<C:\projects\boost-root\libs\gil\test\extension\numeric>"  
```


---

## Review 4 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-07 18:00:53 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-272119619  

Generally it looks good. I just have several minor requests.  
  
Could you add comment documenting any constraints and limitations of the current `convolve_2d` implementation. That is:  
  
![image](https://user-images.githubusercontent.com/80741/62645961-92525380-b94d-11e9-934c-d98fb68fdf3f.png)  
(Source http://www.songho.ca/dsp/convolution/convolution2d_example.html)  
  
 In particular, how it handles the boundary pixels. Ideally, if referenced to/contrasted with/named after the `convolve_1d` modes:  
  
https://github.com/boostorg/gil/blob/8815ab0554d0555e1607a4c70fe1ca8aab9773c4/include/boost/gil/extension/numeric/convolve.hpp#L35-L42  
  
I hope it makes sense.

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 365 |+     int flip_ker_row, flip_ker_col, row_boundary, col_boundary;
 366 |+     float aux_total;
 367 |+     for (std::ptrdiff_t view_row = 0; view_row < src_view.height(); ++view_row)              // rows
```

> Username: mloskot  
> Created_at: 2019-08-07 17:34:28 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311676448  

nitpicking:  
Do we need those `// rows`, `// columns`, etc. comments?  
Isn't the clear and self-descriptive variables naming sufficient to indicate what each of the scopes does?

---

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 415 |+     for (std::size_t i = 0; i < src_view.num_channels(); i++)
 416 |+     {
 417 |+         detail::convolve_2d_imp
```

> Username: mloskot  
> Created_at: 2019-08-07 17:36:19 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311677297  

Indentation of the arguments missing:  
  
```  
detail::convolve_2d_imp(  
    nth_channel_view(src_view, i),  
    nth_channel_view(dst_view, i),  
    kernel);  
```

---

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 361 |+ 
 362 |+ template <typename SrcView, typename DstView, typename Kernel>
 363 |+ void convolve_2d_imp(SrcView const& src_view, DstView const& dst_view, Kernel const& kernel)
```

> Username: mloskot  
> Created_at: 2019-08-07 17:37:24 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311677754  

`convolve_2d_imp` should be renamed to `convolve_2d_impl`


📁 test/extension/numeric/convolve_2d.cpp

```diff
  17 |+ namespace gil = boost::gil;
  18 |+ 
  19 |+ std::uint8_t img[] = {0, 0, 0, 0, 0, 0, 0, 0, 0,
```

> Username: mloskot  
> Created_at: 2019-08-07 17:41:26 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311679509  

Nitpicking: could you align both arrays to the left?  
  
```  
std::uint8_t img[] =  
{  
    0, 0, 0, 0, 0, 0, 0, 0, 0,  
    0, 0, 0, 0, 0, 0, 0, 0, 0,  
    0, 0, 255, 0, 0, 0, 255, 0, 0,  
    0, 0, 0, 255, 0, 255, 0, 0, 0,  
    0, 0, 0, 0, 255, 0, 0, 0, 0,  
    0, 0, 0, 255, 0, 255, 0, 0, 0,  
    0, 0, 255, 0, 0, 0, 255, 0, 0,  
    0, 0, 0, 0, 0, 0, 0, 0, 0,  
    0, 0, 0, 0, 0, 0, 0, 0, 0  
};  
```

---

📁 test/extension/numeric/convolve_2d.cpp

```diff
  41 |+ BOOST_AUTO_TEST_CASE(convolve_2d_test)
  42 |+ {
  43 |+     gil::gray8c_view_t src_view = gil::interleaved_view(9, 9, (const gil::gray8_pixel_t*)img, 9);
```

> Username: mloskot  
> Created_at: 2019-08-07 17:48:06 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311682403  

I know C-style casts are still common across GIL docs and source code, but I'd suggest to use `reinterpret_cast` here.

---

📁 test/extension/numeric/convolve_2d.cpp

```diff
  37 |+                         0, 0, 0, 0, 0, 0, 0, 0, 0 };
  38 |+ 
  39 |+ BOOST_AUTO_TEST_SUITE(convolve_1d)
```

> Username: mloskot  
> Created_at: 2019-08-07 17:48:50 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311682728  

Shouldn't `convolve_1d`  read `convolve_2d`?

---

📁 test/extension/numeric/convolve_2d.cpp

```diff
  39 |+ BOOST_AUTO_TEST_SUITE(convolve_1d)
  40 |+ 
  41 |+ BOOST_AUTO_TEST_CASE(convolve_2d_test)
```

> Username: mloskot  
> Created_at: 2019-08-07 17:52:46 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311684540  

Please, rename `convolve_2d_test` to something more descriptive e.g. `convolve_2d_with_normalized_mean_filter`

---

📁 test/extension/numeric/convolve_2d.cpp

```diff
  52 |+     gil::convolve_2d(src_view, dst_view, kernel);
  53 |+ 
  54 |+     gil::gray8c_view_t out_view = gil::interleaved_view(9, 9, (const gil::gray8_pixel_t*)output, 9);
```

> Username: mloskot  
> Created_at: 2019-08-07 17:53:49 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311685071  

C-style cast to `reinterpret_cast` here too


---

## Comment 5

> Username: miralshah365  
> Created_at: 2019-08-07 22:09:21 UTC  
> Url: https://github.com/boostorg/gil/pull/367#issuecomment-519287074  

> Generally it looks good. I just have several minor requests.  
>   
> Could you add comment documenting any constraints and limitations of the current `convolve_2d` implementation. That is:  
>   
> ![image](https://user-images.githubusercontent.com/80741/62645961-92525380-b94d-11e9-934c-d98fb68fdf3f.png)  
> (Source http://www.songho.ca/dsp/convolution/convolution2d_example.html)  
>   
> In particular, how it handles the boundary pixels. Ideally, if referenced to/contrasted with/named after the `convolve_1d` modes:  
>   
> https://github.com/boostorg/gil/blob/8815ab0554d0555e1607a4c70fe1ca8aab9773c4/include/boost/gil/extension/numeric/convolve.hpp#L35-L42  
>   
> I hope it makes sense.  
  
The current implementation of `convolve_2d` is limited to use `convolve_option_extend_zero` to handle the boundary pixels. This can be considered as one of the limitations of the function unlike `convolve_row`/`col`/`1d` it does not allow various boundary options.

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2019-08-07 22:11:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-272256271  

@miralshah365 LGTM! Feel free to merge as soon as all CI-s are green.

---

## Review 7 [Commented]

> Username: mloskot  
> Created_at: 2019-08-08 09:26:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-272447882  

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 399 |+ } //namespace detail
 400 |+ 
 401 |+ /*!
```

> Username: mloskot  
> Created_at: 2019-08-08 09:26:27 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311939198  

@miralshah365  In future, please prefer the Doxygen's C++ style `///` instead of Javadoc or Qt styles.  
  
Shortly, a recommended structure of a Doxygen-style function comment is this:  
  
```  
/// \brief Short summary, one sentence  
/// ...detailed documentation ...  
/// \tparam T1 - template parameter...  
/// \tparam T2 - template parameter...  
/// \param p1 - function parameter...  
/// \param p2 - function parameter...  
```


---

## Review 8 [Commented]

> Username: mloskot  
> Created_at: 2019-08-08 09:28:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-272448812  

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 400 |+ 
 401 |+ /*!
 402 |+ convolve_2d can only use convolve_option_extend_zero as convolve_boundary_option
```

> Username: mloskot  
> Created_at: 2019-08-08 09:28:01 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311939944  

@miralshah365 Please, don't forget to open an issue with list your ideas and wishes about what can be improved in the `convolve_2d`. It will be useful record.


---

## Review 9 [Commented]

> Username: lpranam  
> Created_at: 2019-08-08 09:46:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-272458958  

📁 include/boost/gil/extension/numeric/convolve.hpp

```diff
 392 |+                 }
 393 |+             }
 394 |+             dst_view(view_col, view_row) = iround(aux_total);
```

> Username: lpranam  
> Created_at: 2019-08-08 09:46:27 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311947942  

Rounding is only required when the channel is of integer type. Due to this statement incorrect/inaccurate results would be produced if the channel is of floating type(i.e : gray32f_pixel_t, rgb32f_pixel_t).

> Username: mloskot  
> Created_at: 2019-08-08 09:50:26 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311949771  

@lpranam Yes, you are correct. At this stage, I allowed @miralshah365 to not to worry about making implemenations 100% generic and to ignore those non-integer pixel types.  
**It would be good if there is** `static_assert` indeed that documents the current limitation.

> Username: lpranam  
> Created_at: 2019-08-08 10:20:30 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311961704  

@mloskot @miralshah365 In addition to my previous comment `convolve_2d` results are not consistent with `convolve_1d`. I believe our results should be consistent with our methods instead of with OpenCV. Inconsistent results are also produced due to `iround`. If we remove it we get results aligned with legacy methods and floating-point type can also be handled.

> Username: mloskot  
> Created_at: 2019-08-08 10:32:29 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311966017  

@lpranam This is an important insight.  
  
I believe, the story is this:  
- @miralshah365 was using OpenCV as reference to verify overall correctness of her implementation, which I also consider a good test.  
- she identified that output values are slightly different and we started discussing what may be the reason  
- I suggested to try `iround` which turned out to be the missing link for the consistency with OpenCV  
  
On reflection, I agree with you that it's better to strive for in-library consistency and  keep `convolve_2d` consistent with `convolve_1d`.  
  
Having `convovle_2d` tested against OpenCV, we can assume it is correct.  
Having fiddled with the rounding, we understand where slight differences in the results come from.  
So, I think, we can take step back and bring the within GIL consistency.  
  
@miralshah365 What do you think?

> Username: miralshah365  
> Created_at: 2019-08-08 11:59:50 UTC  
> Updated_at: 2019-08-08 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r311996289  

I completely agree with having consistency withing library. In producing matching results with other libraries I forgot to check it with GIL. I have updated the code as suggested.

> Username: mloskot  
> Created_at: 2019-08-08 12:53:42 UTC  
> Updated_at: 2019-08-08 12:53:43 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r312018182  

@miralshah365   
>  I forgot to check it with GIL  
  
Does it mean you have now compared the `convolve_1d` vs `convolve_2d` for the same input?

> Username: miralshah365  
> Created_at: 2019-08-08 14:05:09 UTC  
> Updated_at: 2019-08-08 14:06:07 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r312053804  

Yes, now I have compared.   
  
```  
#include <vector>  
#include <iostream>  
#include <boost/gil/extension/numeric/kernel.hpp>  
#include <boost/gil/extension/numeric/convolve.hpp>  
#include <boost/gil/extension/io/png.hpp>  
  
#include <boost/gil/extension/io/jpeg.hpp>  
using namespace boost::gil;  
using namespace std;  
int main()  
{  
    gray8_image_t img;  
    read_image("src_view.png", img, png_tag{});  
    gray8_image_t img_out(img.dimensions()), img_out1(img.dimensions());  
  
    std::vector<float> v(9, 1.0f / 9.0f);  
    kernel_2d<float> kernel(v.begin(), v.size(), 1, 1);  
    convolve_2d(view(img), kernel, view(img_out1));  
  
    write_view("out-convolve2d.png", view(img_out1), jpeg_tag{});  
  
  
    //------------------------------------//  
    std::vector<float> v1(3, 1.0f / 3.0f);  
    kernel_1d<float> kernel1(v1.begin(), v1.size(), 1);  
    convolve_1d<gray32f_pixel_t>(const_view(img), kernel1, view(img_out), convolve_boundary_option::convolve_option_extend_zero);  
  
    write_view("out-convolve_option_extend_zero.png", view(img_out), png_tag{});  
  
    if (equal_pixels(view(img_out1), view(img_out)))cout << "convolve_option_extend_zero" << endl;  
  
    cout << "done\n";  
    cin.get();  
  
    return 0;  
}  
  
```

> Username: mloskot  
> Created_at: 2019-08-08 16:47:23 UTC  
> Updated_at: 2019-08-08 16:47:24 UTC  
> Url: https://github.com/boostorg/gil/pull/367#discussion_r312137188  

@miralshah365 Right, since it's an example, I missed that as a check.  
  
Once this is merged, I will update the tests so the 1D convolution is also verified using the `X` image defined in `std::uint8_t img[]` and `std::uint8_t output[]`.


---

## Review 10 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-08 10:33:32 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-272482934  

@miralshah365 Let's resolve this new discussion with @lpranam  before merging https://github.com/boostorg/gil/pull/367#pullrequestreview-272458958  
  
Also, you need to update your branch and resolve the little conflict in the build scripts.

---

## Review 11 [Approved]

> Username: mloskot  
> Created_at: 2019-08-08 16:48:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/367#pullrequestreview-272702934  

@miralshah365 LGTM. Feel free to merge.  
  
@lpranam Thank you for offering valuable feedback!

---

## Comment 12

> Username: mloskot  
> Created_at: 2019-08-08 16:49:30 UTC  
> Updated_at: 2019-08-08 16:49:46 UTC  
> Url: https://github.com/boostorg/gil/pull/367#issuecomment-519599648  

@miralshah365  also please don't forget about https://github.com/boostorg/gil/pull/367#discussion_r311939944

---
