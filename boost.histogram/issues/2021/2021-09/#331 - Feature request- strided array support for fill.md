# #331 - Feature request: strided array support for fill? [Open]

> Username: henryiii  
> Created at: 2021-09-15 02:51:55 UTC  
> Updated at: 2021-09-26 13:59:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/331  

In #327, the bug seems to be that single values are not supported correctly. In https://github.com/scikit-hep/hist/issues/303, scalars are currently not allowed for samples - in working on this, I found that `h.fill(0.3, sample=[1, 2], weight=1)` is not supported in boost-histogram, due to the spans not matching (may be solvable on the boost-histogram side).  
  
A way to support this easily could be to support strided arrays, like NumPy, Eigen, Boost.MultiArray, etc. all provide. If an array is described by a stride and an offset, then setting that stride to 0 would allow a scalar value without having a scalar datatype. Then there would be half as many items in the fill variant, just arrays, no scalars anymore.  
  
A related issue is that boost-histogram has to copy the input array if it's not already a dense 1D array. If describing an array with a starting pointer, stride, and offset was possible, then this would no longer be necessary, only the datatype would have to match to avoid a copy.  
  
Thoughts?

---

## Comment 1

> Username: HDembinski  
> Created at: 2021-09-26 11:16:38 UTC  
> Updated at: 2021-09-26 11:16:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/331#issuecomment-927287300  

`h.fill(0.3, sample=[1, 2], weight=1)` should be supported in C++, but currently it is not. What is already supported is `h.fill(0.3, [1, 2])` for a multidimensional histogram. This support just does not currently extend to `sample` and `weight`, but it should.  
  
Supporting strided arrays in general is much more involved than this special case, which is easy to support without supporting strided input.

---

## Comment 2

> Username: HDembinski  
> Created at: 2021-09-26 13:59:25 UTC  
> Updated at: 2021-09-26 13:59:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/331#issuecomment-927311146  

What I am currently doing internally is smarter than just setting the stride to 0 if the user passes a value instead of an iterable. A naive implementation would call `axis::index(value)` N times, but I am calling it exactly once in this case.
