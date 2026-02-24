# #725 fix: Normalize Gaussian 2D kernel. [Merged]

> Username: cgringmuth  
> Created at: 2023-02-01 12:00:45 UTC  
> Updated at: 2023-03-31 07:57:06 UTC  
> Merged at: 2023-02-07 13:11:52 UTC  
> Closed at: 2023-02-07 13:11:52 UTC  
> Url: https://github.com/boostorg/gil/pull/725  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Gaussian kernel does not sum up to 1 which leads to darkening of image after convolution.  
  
I also rewrote a little the code. Especially `const float value = static_cast<float>(nominator / denominator);` which should be of type `T` in my opinion. Otherwise value is always float, independent of `T`.  
  
### References  
  
> When converting the Gaussian’s continuous values into the discrete values needed for a kernel, the sum of the values will be different from 1. This will cause a darkening or brightening of the image. To remedy this, the values can be normalized by dividing each term in the kernel by the sum of all terms in the kernel.   
  
from [wiki](https://en.wikipedia.org/wiki/Gaussian_blur)  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2023-02-06 08:55:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/725#pullrequestreview-1284777423  

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 138 |     const double denominator = 2 * boost::gil::detail::pi * sigma * sigma;
 139 |-     auto middle = side_length / 2;
 139 |+     const auto middle = side_length / 2;
```

> Username: mloskot  
> Created_at: 2023-02-06 08:55:03 UTC  
> Url: https://github.com/boostorg/gil/pull/725#discussion_r1097096832  

Nitpick: [Join the East Const Revolution!](https://hackingcpp.com/cpp/design/east_vs_west_const.html) ;-)


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2023-02-06 08:59:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/725#pullrequestreview-1284783544  

I made one request related to, I think, unnecessary re-formatting.

📁 include/boost/gil/image_processing/numeric.hpp

```diff
 151 |+         values[y * side_length + x] = value;
 152 |+         sum += value;
 153 |+       }
```

> Username: mloskot  
> Created_at: 2023-02-06 08:58:39 UTC  
> Updated_at: 2023-02-06 08:59:14 UTC  
> Url: https://github.com/boostorg/gil/pull/725#discussion_r1097101282  

Unless this re-formatting is required, I'd suggest to avoid such changes in bugfixing PR-s.  
  
By the way, we have https://github.com/boostorg/gil/blob/develop/example/clang-format/.clang-format

> Username: cgringmuth  
> Created_at: 2023-02-07 08:16:28 UTC  
> Url: https://github.com/boostorg/gil/pull/725#discussion_r1098315695  

Good point, thx for the remark. I guess clion did this for me. Will revert.

> Username: cgringmuth  
> Created_at: 2023-02-07 08:45:34 UTC  
> Updated_at: 2023-02-07 08:45:35 UTC  
> Url: https://github.com/boostorg/gil/pull/725#discussion_r1098348616  

I wasn't sure if you meant the pure reformatting (e.g. indentation etc) or also the rewrite of `delta_x/y` and `const auto value = static_cast<T>(nominator / denominator);` ... so I just reverted the pure formatting change.   
  
Btw. If I apply the clang-format file to this file, it change quite a lot actually.

> Username: mloskot  
> Created_at: 2023-02-07 09:16:07 UTC  
> Url: https://github.com/boostorg/gil/pull/725#discussion_r1098384373  

I meant the 4 spaces to 2 spaces change.  
  
Yeah, we haven't completed the clang-format application yet, so automated formatting is in a bit of a mess.  
Since we're going to do the big reformat sooner or later, then don't bother with the accidental formatting your IDE.   
IOW, ignore my request please.


---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2023-02-07 09:16:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/725#pullrequestreview-1286696199  

LGTM, thanks!

---
