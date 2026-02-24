# #562 Add higher order kernel support for Sobel operator [Open]

> Username: meshtag  
> Created at: 2021-02-19 17:04:46 UTC  
> Updated at: 2025-09-06 04:15:39 UTC  
> Url: https://github.com/boostorg/gil/pull/562  

### Description  
  
This pull request intends to automate the process of kernel generation for higher order Sobel derivatives. Generated kernels were cross checked with opencv, test cases have been provided for comparing the obtained kernels with opencv kernels upto 6th order sobel derivative (13x13 dimensional). I used the following piece of code for obtaining opencv kernels.  
```  
import numpy as np  
import cv2  
  
# In variable name "sobelnx", n denotes dimensions of the nxn kernel which is to be obtained while "x" signifies that the   
# derivative was calculated in horizontal direction. Similar terminology is used for derivative kernels with respect to the vertical  
# direction.   
  
sobel5x = cv2.getDerivKernels(0, 1, 5)  
sobel7x = cv2.getDerivKernels(0, 1, 7)  
sobel9x = cv2.getDerivKernels(0, 1, 9)  
sobel11x = cv2.getDerivKernels(0, 1, 11)  
sobel13x = cv2.getDerivKernels(0, 1, 13)  
  
sobel5y = cv2.getDerivKernels(1, 0, 5)  
sobel7y = cv2.getDerivKernels(1, 0, 7)  
sobel9y = cv2.getDerivKernels(1, 0, 9)  
sobel11y = cv2.getDerivKernels(1, 0, 11)  
sobel13y = cv2.getDerivKernels(1, 0, 13)  
  
print(np.outer(sobel5x[0], sobel5x[1]))  
print(np.outer(sobel7x[0], sobel7x[1]))  
print(np.outer(sobel9x[0], sobel9x[1]))  
print(np.outer(sobel11x[0], sobel11x[1]))  
print(np.outer(sobel13x[0], sobel13x[1]))  
  
print(np.outer(sobel5y[0], sobel5y[1]))  
print(np.outer(sobel7y[0], sobel7y[1]))  
print(np.outer(sobel9y[0], sobel9y[1]))  
print(np.outer(sobel11y[0], sobel11y[1]))  
print(np.outer(sobel13y[0], sobel13y[1]))  
```  
I have kept the upper limit for dimensions of generated kernel as 31x31 (15th order sobel derivative), I think this limit can be further extended depending upon the execution time taken by other processes of the algorithm apart from kernel generation.   
  
### References  
  
https://stackoverflow.com/a/10032882/14958679  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-02-19 18:45:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/562#pullrequestreview-594456088  

First, all CI builds need to pass for this PR.  
  
I also left a few initial suggestions.

📁 include/boost/gil/detail/math.hpp

```diff
  12 | #include <array>
  13 | #include <boost/gil/extension/numeric/kernel.hpp>
  14 |+ #include <vector>
```

> Username: mloskot  
> Created_at: 2021-02-19 18:31:03 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579391634  

Please, [bundle the C++ standard headers together](https://github.com/boostorg/gil/wiki/Include-Directives-Order)

---

📁 include/boost/gil/detail/math.hpp

```diff
  19 |+ {
  20 |+     sobeldx,
  21 |+     sobeldy,
```

> Username: mloskot  
> Created_at: 2021-02-19 18:31:21 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579391820  

I'd prefer `sobel_dx` and `sobel_dy` here

---

📁 include/boost/gil/detail/math.hpp

```diff
  30 | static constexpr std::array<float, 9> dx_scharr = {{-1, 0, 1, -1, 0, 1, -1, 0, 1}};
  31 | static constexpr std::array<float, 9> dy_sobel = {{1, 2, 1, 0, 0, 0, -1, -2, -1}};
  32 |+ static constexpr std::array<float, 25> dy_sobel2 = {{
```

> Username: mloskot  
> Created_at: 2021-02-19 18:31:58 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579392170  

What does `2` stand for in `sobel2`?

> Username: meshtag  
> Created_at: 2021-02-27 12:36:04 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r584116207  

```2``` in ```dy_sobel2``` indicates that the order of Sobel derivative in y-direction is 2, similar terminology is used for ```dx_sobel2```. I have added comments for clarifying this to future readers, is this resolvable now?

> Username: mloskot  
> Created_at: 2021-02-27 19:30:38 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r584178714  

Good

---

📁 include/boost/gil/detail/math.hpp

```diff
  45 | 
  46 |+ // Please refer https://stackoverflow.com/a/10032882/14958679 for getting an overview of the
  47 |+ // concept applied for obtaining higher order sobel kernels.
```

> Username: mloskot  
> Created_at: 2021-02-19 18:33:03 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579392800  

It should read `Sobel kernels` after Irwin Sobel

---

📁 include/boost/gil/detail/math.hpp

```diff
  70 |+                                                          std::vector<float>(convolved_kernel_size)),
  71 |+                                     dummy_kernel(convolved_kernel_size,
  72 |+                                                          std::vector<float>(convolved_kernel_size));
```

> Username: mloskot  
> Created_at: 2021-02-19 18:33:38 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579393133  

This is too much blank fields to the left

---

📁 include/boost/gil/detail/math.hpp

```diff
  64 |+ /// \tparam T2 - Type of second argument for kernel vector convolution.
  65 |+ template<typename T1, typename T2>
  66 |+ std::vector<std::vector<float>> kernel_convolve_impl(T1 kernel1, T2 kernel2)
```

> Username: mloskot  
> Created_at: 2021-02-19 18:34:23 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579393581  

Nitpick: To me, personally,  this nicely aligns  
  
```cpp  
auto kernel_convolve_impl(T1 kernel1, T2 kernel2) -> std::vector<std::vector<float>>  
```

---

📁 include/boost/gil/detail/math.hpp

```diff
  80 |+     
  81 |+     std::ptrdiff_t padding_reference = (kernel2.size() - 1) / 2;
  82 |+     for(std::ptrdiff_t row = padding_reference;row < dummy_kernel.size() - padding_reference; ++row)
```

> Username: mloskot  
> Created_at: 2021-02-19 18:37:22 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579395353  

Missing `{` and `}` for the outer loop.

---

📁 include/boost/gil/detail/math.hpp

```diff
  79 |+     // to create 'dummy_kernel'.
  80 |+     
  81 |+     std::ptrdiff_t padding_reference = (kernel2.size() - 1) / 2;
```

> Username: mloskot  
> Created_at: 2021-02-19 18:37:55 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579395726  

I'm not sure the `reference` wording is clear here. Why not just `padding`?

> Username: meshtag  
> Created_at: 2021-02-27 12:26:01 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r584115203  

Here ```padding``` refers to the strip of zeros created around ```kernel1``` which is required for our intended convolution. The variable ```padding_reference``` is meant to be used as a reference for finding the location of blocks from where padding starts inside ```dummy_kernel```. Shall I change the name of that variable from ```padding_reference``` to ```padding```?

> Username: mloskot  
> Created_at: 2021-02-27 19:29:17 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r584178546  

> from where padding starts   
  
What about `padding_origin` then?

---

📁 include/boost/gil/detail/math.hpp

```diff
  87 |+         }
  88 |+ 
  89 |+     std::ptrdiff_t flip_ker_row, flip_ker_col, row_boundary, col_boundary;
```

> Username: mloskot  
> Created_at: 2021-02-19 18:38:15 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579395936  

`s/ker/kernel/`

> Username: meshtag  
> Created_at: 2021-02-27 12:45:27 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r584117325  

I am not sure if I understood this correctly, shall I rename ```flip_ker_row``` to ```flip_kernel_row```?

> Username: mloskot  
> Created_at: 2021-02-27 13:12:48 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r584120593  

Yes, please - full words are preferred


📁 include/boost/gil/image_processing/numeric.hpp

```diff
 257 |+                                                                        "they are not yet provided");
 258 |     //to not upset compiler
 259 |     throw std::runtime_error("unreachable statement");
```

> Username: mloskot  
> Created_at: 2021-02-19 18:40:47 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r579397436  

Why not just replace this `runtime_error` with the `length_error` above?


---

## Comment 2

> Username: meshtag  
> Created_at: 2021-02-20 04:43:45 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-782559397  

@mloskot , I have my mid-semester examinations in next week and I don't think I will be able to invest time . I will probably be back again on 27-28 th February once they are over. For this reason , I am converting this pull request to draft. Apologies for not doing this earlier.  
PS : I will have to dig in for checking what exactly is going wrong with builds since it is working fine on my system. I intend to do that with a clear and calm mind after my mid-terms. Apologies again for the delay.

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-02-20 07:30:43 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-782578863  

@meshtag No need to apologise. We're all volunteers here. Good luck with your exams.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2021-02-28 19:30:29 UTC  
> Updated_at: 2025-09-06 04:15:39 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-787508313  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/562?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `95.29412%` with `4 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 79.08%. Comparing base ([`0c0fe1a`](https://app.codecov.io/gh/boostorg/gil/commit/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`155de1c`](https://app.codecov.io/gh/boostorg/gil/commit/155de1cc00679fb2a9f793667626b6a023482dde?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 226 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #562      +/-   ##  
===========================================  
+ Coverage    77.81%   79.08%   +1.27%       
===========================================  
  Files          110      118       +8       
  Lines         4367     5101     +734       
===========================================  
+ Hits          3398     4034     +636       
- Misses         969     1067      +98       
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 5 [Approved]

> Username: mloskot  
> Created_at: 2021-03-01 20:42:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/562#pullrequestreview-601147580  

LGTM  
  
/cc @simmplecoder  @lpranam

---

## Comment 6

> Username: lpranam  
> Created_at: 2021-03-02 03:06:05 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-788541003  

will have a look tonight :)

---

## Comment 7

> Username: meshtag  
> Created_at: 2021-03-02 12:56:40 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-788888923  

Thanks @mloskot for the approval.  
I was wondering whether we should increase the upper bound for dimensions of the obtained kernel. The current upper bound for dimensions is 31x31(15th order derivative), I believe this can be fairly extended.

---

## Comment 8

> Username: simmplecoder  
> Created_at: 2021-03-02 16:05:14 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-789018088  

@meshtag there is a way to improve performance by using associativity property of convolution.  
  
Lets say you have some base kernel `k` and you want to raise it to power `p`, the easiest way is:  
  
`p * p * p *p * ...` k times.  
  
What I'm proposing will make `log(p)` convolutions instead by recursively multiplying results from previous steps:  
  
`r(p) = r(p / 2) * r(p / 2)` where * is convolution.  
  
You could use this to get to the closest power of two that is not greater than the target.

---

## Comment 9

> Username: simmplecoder  
> Created_at: 2021-03-02 16:19:39 UTC  
> Updated_at: 2021-03-02 16:35:13 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-789028239  

@meshtag I believe it should also be possible to use some container like `std::vector`, use `gil::interleaved_view` or similar, use already existing convolution on image views, then just put the resulting container inside a gil kernel. That way we might have less code duplication and when the optimizations arrive from GSoC this functionality will automatically benefit from it. Perhaps even `std::move` it inside, so no copying will take place.  
  
If there are changes like padding required for it to work, perhaps we should fix the image view convolution instead? What do you think (@mloskot @lpranam )?

---

## Comment 10

> Username: meshtag  
> Created_at: 2021-03-02 19:53:41 UTC  
> Updated_at: 2021-03-02 19:54:45 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-789171604  

@simmplecoder ,  
Regarding your first comment,   
Great Idea ! I can simply use logarithm with base 2 for implementing it (will be implemented probably in my next commit).  
  
Regarding the second comment,   
I think the major issue in generalizing convolution implementation for ```vectors``` as well as ```image views``` is the difference in their methods used for accessing individual elements.  
Moreover, we require a data structure whose size can vary efficiently during runtime , one option is to use ```dynamic image views``` but I am not sure if they would perform better than ```STL vector``` which is currently used , please do share your opinion on this .

---

## Comment 11

> Username: mloskot  
> Created_at: 2021-03-02 21:34:38 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-789235101  

@simmplecoder The optimisation ideas are interesting, but unless they are trivial, I don't mind if those are developed after the merge, in separate PRs.

---

## Review 12 [Changes requested]

> Username: mloskot  
> Created_at: 2021-03-03 20:02:42 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/562#pullrequestreview-603292527  

📁 include/boost/gil/detail/math.hpp

```diff
  74 |+     std::vector<std::vector<float>> convolved_kernel(convolved_kernel_size,
  75 |+         std::vector<float>(convolved_kernel_size)),dummy_kernel(convolved_kernel_size,
  76 |+         std::vector<float>(convolved_kernel_size));
```

> Username: mloskot  
> Created_at: 2021-03-03 19:58:10 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r586736535  

Please, put these into separate lines with `;`

---

📁 include/boost/gil/detail/math.hpp

```diff
  96 |+ 
  97 |+     std::ptrdiff_t flip_kernel_row, flip_kernel_col, row_boundary, col_boundary;
  98 |+     float aux_total;
```

> Username: mloskot  
> Created_at: 2021-03-03 19:58:55 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r586737034  

` = 0.0f;` for hygiene and to make sensitive analysers happy :)

---

📁 include/boost/gil/detail/math.hpp

```diff
  69 |+ /// \tparam T2 - Type of second argument for kernel vector convolution.
  70 |+ template<typename T1, typename T2>
  71 |+ static auto kernel_convolve_impl(T1 kernel1, T2 kernel2) -> std::vector<std::vector<float>>
```

> Username: mloskot  
> Created_at: 2021-03-03 20:00:06 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r586737840  

It's a free function, in a header, in `detail` namespace, what is the `static` for?

> Username: meshtag  
> Created_at: 2021-03-04 07:12:07 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r587206942  

I was not using ```static``` initially which caused all builds to fail. After an exhaustive search inside raw logs, I came across the following line ,    
```  
2021-03-04T04:26:54.6586533Z sample_image.cpp:(.text+0x0): multiple definition of `boost::gil::detail::kernel_vector_fill(std::vector<std::vector<float, std::allocator<float> >, std::allocator<std::vector<float, std::allocator<float> > > >&, boost::gil::detail::kernel_type)'  
```  
This lead me to believe that the compiler was considering different definitions of ```kernel_vector_fill``` whenever it was parsing through it. For tackling this, the immediate solution which came to my mind was using ```static```. I wonder if that was the best way to solve it.   
[Here](https://github.com/meshtag/gil/runs/2027943906) is a link to those ```build results``` for your reference.  
Note : I didn't encounter this problem on my system. I used ```cmake``` on  ```Ubuntu 20.04 OS```for build.

> Username: meshtag  
> Created_at: 2021-03-18 15:22:56 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r596974328  

@mloskot, is this resolvable?

> Username: mloskot  
> Created_at: 2021-03-18 18:32:03 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597140666  

You should be `inline` instead, I think.

> Username: meshtag  
> Created_at: 2021-03-19 06:08:10 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597427648  

I googled this a bit and I think I now understand your reason. I am attaching this [link](https://stackoverflow.com/questions/10492847/c-namespace-functions/10493005#10493005) here just in case if someone questions why I made it inline despite the function being large in size and its execution time is not lesser than its call time.

> Username: mloskot  
> Created_at: 2021-03-19 16:55:06 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597834196  

Yes, I think this sums it up well:  
["Only short functions should be inlined" is a rule of thumb, but there are so many exceptions and so many other more important reasons why you should or should not inline a function so as to make the rule of thumb almost useless.](https://stackoverflow.com/questions/3973218/header-only-libraries-and-multiple-definition-errors#comment4250148_3973236)  
  
So, we agree about making it `inline`?

> Username: meshtag  
> Created_at: 2021-03-19 18:58:25 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597910762  

No objections from my side.

> Username: mloskot  
> Created_at: 2021-03-19 19:13:01 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597918407  

Please update the PR then

---

📁 include/boost/gil/detail/math.hpp

```diff
 168 |+     kernel_vector_fill(convolved_kernel, type);
 169 |+ 
 170 |+     std::vector<std::vector<float>>smoothing_dummy = smoothing_kernel;
```

> Username: mloskot  
> Created_at: 2021-03-03 20:01:08 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r586738513  

`>s` space missing

---

📁 include/boost/gil/detail/math.hpp

```diff
 173 |+     // 'smoothing_dummy' with itself. This number when used as a power of 2 in its exponentiation,
 174 |+     // will result in a number which is the largest power of 2 smaller than 'order - 2'.
 175 |+     unsigned int smooth_repetition = (unsigned int)log2(order - 2);
```

> Username: mloskot  
> Created_at: 2021-03-03 20:01:47 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r586738880  

- `std::` missing for `log2`  
- `static_cast` is preferred to C-style cast  
- make the variable `const` unless it's modified

---

📁 include/boost/gil/detail/math.hpp

```diff
 184 |+     // Variable 'order_decrease' will store the amount of decrease in order obtained due to the above 
 185 |+     // optimization. It stores the largest power of 2 smaller than 'order - 2'.
 186 |+     unsigned int order_decrease = pow(2, smooth_repetition);
```

> Username: mloskot  
> Created_at: 2021-03-03 20:02:36 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r586739348  

missing `std::` for `pow`

> Username: meshtag  
> Created_at: 2021-03-04 07:00:33 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r587202222  

I have implemented this change. Just for educational purposes, are we making this change to make the code more adept into c++11 or is it somehow related to performance?

> Username: mloskot  
> Created_at: 2021-03-18 18:03:45 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597120865  

Well, this code is in public headers, so we need to keep it clean ~C++11~ C+14


---

## Comment 13

> Username: meshtag  
> Created_at: 2021-03-04 13:20:46 UTC  
> Updated_at: 2021-03-06 12:31:55 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-790613094  

@simmplecoder , I have implemented changes suggested by you for improving performance. Please do give it a look when you get time.

---

## Review 14 [Changes requested]

> Username: mloskot  
> Created_at: 2021-03-18 18:32:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/562#pullrequestreview-615732868  

The new code seems to introduce number of warnings that need to be avoided.

📁 include/boost/gil/detail/math.hpp

```diff
 191 |+     }
 192 |+ 
 193 |+     for(std::ptrdiff_t row = 0;row < convolved_kernel.size(); ++row)
```

> Username: mloskot  
> Created_at: 2021-03-18 18:23:01 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597134452  

`static_cast<std::ptrdiff_t>(convolved_kernel.size())`

> Username: meshtag  
> Created_at: 2021-03-19 06:14:32 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597429751  

Apparently, @simmplecoder is attempting a patch based on the current commit as mentioned [here](https://cpplang.slack.com/archives/CSVT0STV2/p1615702000071100?thread_ts=1615616569.056600&cid=CSVT0STV2).  
I will apply these changes in a separate commit once I get to know about the current status of his work. For this reason, I am not resolving your comments now, will do it once I apply suggested changes.

> Username: mloskot  
> Created_at: 2021-03-19 16:56:31 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597835277  

Right, then this can make it in before his work is completed, but then we will have to remember to review these places.

> Username: meshtag  
> Created_at: 2021-03-19 19:02:41 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597913058  

Or is it a better idea to wait for the patch to be created and then apply these changes after applying the patch, meanwhile keeping these comments as unresolved(just so that I don't forget them).

> Username: mloskot  
> Created_at: 2021-03-19 19:13:49 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597918819  

Do as you prefer

---

📁 include/boost/gil/detail/math.hpp

```diff
  71 |+ static auto kernel_convolve_impl(T1 kernel1, T2 kernel2) -> std::vector<std::vector<float>>
  72 |+ {
  73 |+     size_t convolved_kernel_size = kernel1.size() + kernel2.size() - 1;
```

> Username: mloskot  
> Created_at: 2021-03-18 18:23:40 UTC  
> Updated_at: 2021-04-01 13:29:52 UTC  
> Url: https://github.com/boostorg/gil/pull/562#discussion_r597134888  

`convolved_kernel_size` should rather be `std::ptrdiff_t`


---

## Comment 15

> Username: meshtag  
> Created_at: 2021-04-01 15:18:01 UTC  
> Updated_at: 2021-04-01 15:18:30 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-811979511  

> error: toolset gcc initialization:  
> error: version '8' requested but 'g++-8' not found and version '7.5.0' of default 'g++' does not match  
> error: initialized from  
> /home/runner/work/gil/boost-root/tools/build/src/build/toolset.jam:44: in toolset.using from module toolset  
> /home/runner/work/gil/boost-root/tools/build/src/build-system.jam:543: in process-explicit-toolset-requests from module build-> > system  
> /home/runner/work/gil/boost-root/tools/build/src/build-system.jam:610: in load from module build-system  
> /home/runner/work/gil/boost-root/tools/build/src/kernel/modules.jam:295: in import from module modules  
> /home/runner/work/gil/boost-root/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
> /home/runner/work/gil/boost-root/boost-build.jam:17: in module scope from module  
  
@mloskot , I encountered above error in [this](https://github.com/boostorg/gil/pull/562/checks?check_run_id=2246393722) build and I am rather suspicious, is this related to changes made by me?

---

## Comment 16

> Username: mloskot  
> Created_at: 2021-04-01 16:31:29 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-812025806  

> I encountered above error in [this](https://github.com/boostorg/gil/pull/562/checks?check_run_id=2246393722) build and I am rather suspicious, is this related to changes made by me?  
  
This failure does not seem to be related to any of your changes in this PR.  
Something is fishy in the CI infrastructure and I will try to look into that tonight.

---

## Comment 17

> Username: meshtag  
> Created_at: 2021-04-03 03:00:30 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-812800308  

@mloskot  @lpranam , I think this PR was accidentally closed, can you please reopen it.

---

## Comment 18

> Username: meshtag  
> Created_at: 2021-04-03 05:23:50 UTC  
> Updated_at: 2021-04-03 05:28:18 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-812814497  

I am working to add support for specifying desired Sobel derivative as well as desired dimensions of the derivative kernel. This will help users to gain more control on the process. Since it will be more feature complete with extra test cases and will have some changes in logic/process, documentation as well as API, I would prefer creating a new PR for it(Please keep this one closed).  
Will that be fine?

---

## Comment 19

> Username: mloskot  
> Created_at: 2021-04-03 09:54:07 UTC  
> Url: https://github.com/boostorg/gil/pull/562#issuecomment-812842720  

Sorry, my commit auto-closed this PR by accident.  
  
Yes, the feature completeness sounds good,must have tests and ideally with docs.

---
