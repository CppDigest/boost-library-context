# #240 - adjacent_transform_iterator has an invalid memory access on some implementations [Closed]

> Username: KitsuneSan89  
> Created at: 2014-08-19 19:39:48 UTC  
> Updated at: 2014-08-22 02:25:27 UTC  
> Closed at: 2014-08-22 02:25:12 UTC  
> Url: https://github.com/boostorg/compute/issues/240  

Because adjacent_transform_iterator uses the ternary conditional for its base-case,   
(e.g. "index == 0 ? input[index]-0 : input[index] - input[index-1]") both possible selections may be evaluated by the implementation (behavior equivalent to select(condition, true_value, false_valve)), leading to an out-of-bounds access when index == 0.   
  
In particular, this causes test_adjacent_difference and test_adjacent_transform iterator to segfault on Intel OpenCL on Linux.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-20 02:12:43 UTC  
> Url: https://github.com/boostorg/compute/issues/240#issuecomment-52726105  

Thanks for testing this! And also, thanks for the hint (which to me seems like a compiler bug to not short-circuit but we should fix it anyway). This seems related to #209. I'll look into it more.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-08-21 06:12:45 UTC  
> Url: https://github.com/boostorg/compute/issues/240#issuecomment-52882168  

This should be fixed in PR #247 (`adjacent_transform_iterator` has been removed and the algorithms that were using it have been rewritten). Can you test it out and let me know if the tests pass for you?

---

## Comment 3

> Username: KitsuneSan89  
> Created at: 2014-08-21 21:00:53 UTC  
> Url: https://github.com/boostorg/compute/issues/240#issuecomment-52983859  

Yes, this commit fixes the issue. All the tests are now passing on my system.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-08-22 02:25:27 UTC  
> Url: https://github.com/boostorg/compute/issues/240#issuecomment-53014867  

Awesome! Thanks!
