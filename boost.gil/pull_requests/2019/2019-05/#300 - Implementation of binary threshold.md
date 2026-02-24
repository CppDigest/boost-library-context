# #300 Implementation of binary threshold [Closed]

> Username: miralshah365  
> Created at: 2019-05-11 18:20:12 UTC  
> Updated at: 2019-05-26 20:38:25 UTC  
> Closed at: 2019-05-26 18:57:16 UTC  
> Url: https://github.com/boostorg/gil/pull/300  

### Description  
  
*Two variant of Binary threshold Implemented*  
1. **Simple Binary Threshold**-  
If the pixel value is more than the threshold then the pixel value is set to maximum or else to 0  
2. **Inverse Binary Threshold**  
If the pixel value is more than the threshold then the pixel value is set to 0 or else to maximum  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-05-20 19:55:08 UTC  
> Updated_at: 2019-05-20 19:55:54 UTC  
> Url: https://github.com/boostorg/gil/pull/300#issuecomment-494127862  

@miralshah365 Thanks for applying the basic changes. We can now focus on discussing the design and the implementation.  
  
I'm not convinced about the idea of  enum-based dispatching to `threshold<gil::BINARY>` and `threshold<gil::BINARY_INV>`.  Unless you have a strong reason to keep the function mode selectable  via the parameters, I'd replace it with separate named functions: `threshold_binary` and `threshold_binary_inverse`.  
  
To give analogy, I find it much clearer that `std::string` offers dedicated specialised `find`, `find_first_of`, find_first_not_of`, etc. instead of single `find` that takes additional parameter, an enumerator that indicates the mode of the find operation.  
  
I certainly would be interested to hear what approach is preferred by @stefanseefeld

---

## Comment 2

> Username: miralshah365  
> Created_at: 2019-05-21 14:55:50 UTC  
> Updated_at: 2019-05-21 14:56:01 UTC  
> Url: https://github.com/boostorg/gil/pull/300#issuecomment-494427130  

I find this analogy more convenient because it has only one API which works for all. Apart from this, I don't have any other reason to use this.   
  
Maybe we can keep this and also add the functions which you suggested. It won't take more than a couple of lines.

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-05-21 15:15:04 UTC  
> Updated_at: 2019-05-21 15:18:12 UTC  
> Url: https://github.com/boostorg/gil/pull/300#issuecomment-494435068  

My point is that we are trying to establish some general pattern to follow for interface design, code structure and naming public functions.  
  
Selecting algorithms and strategies via arguments, typically `bool` or `enum` values, is a tempting idea at first, but it has tendency to proliferate interface with arguments and their combinations (and their checks), some arguments could be unused for some modes, etc.  
  
IMO, Boost.Geometry is a good example here. Let's consider the following functions to calculate distance using various algorithms, each (potentially) can have its own distinct set of arguments:  
  
- [distance](https://www.boost.org/libs/geometry/doc/html/geometry/reference/algorithms/distance/distance_2.html)  
- [comparable_distance](https://www.boost.org/libs/geometry/doc/html/geometry/reference/algorithms/distance.html)  
- [discrete_frechet_distance](https://www.boost.org/libs/geometry/doc/html/geometry/reference/algorithms/discrete_frechet_distance.html)  
- [discrete_hausdorff_distance](https://www.boost.org/libs/geometry/doc/html/geometry/reference/algorithms/discrete_hausdorff_distance.html)  
  
I argue that It's much clearer than single `distance` with arguments to control all possible modes.  
  
-----  
  
Next, once there is `binary_threshold` and `inverse_binary_threshold` (or similar), it may become clearer there is no need for base class to hold the parameters. Perhaps simple lambda will do the job?

---

## Comment 4

> Username: miralshah365  
> Created_at: 2019-05-21 15:35:14 UTC  
> Url: https://github.com/boostorg/gil/pull/300#issuecomment-494443310  

It makes sense to me now. It will make code cleaner too. I'll change it.

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-05-25 22:37:22 UTC  
> Url: https://github.com/boostorg/gil/pull/300#issuecomment-495953592  

@miralshah365   
This goes in the right direction.  
  
You may need to review the code a bit against https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines (e.g. limit the lines length).  
  
Since the two functions have nearly identical structure, I'd suggest to extract the common bits into `namespace detail` as `detail::binary_threshold_impl`. Then have the `binary_threshold` and `inverse_binary_threshold` calling `detail::binary_threshold_impl` and passing the lambda operators.  
  
Finally, I'd like to resubmit your `threshold` branch as new PR to https://github.com/BoostGSoC19/gil-miral as this is part of the project, isn't it? So, let's close this PR as unmerged.

---

## Comment 6

> Username: mloskot  
> Created_at: 2019-05-26 20:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/300#issuecomment-496029760  

The PR has moved to https://github.com/BoostGSoC19/gil-miral/pull/1

---
