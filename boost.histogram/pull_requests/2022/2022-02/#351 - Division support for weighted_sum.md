# #351 Division support for weighted_sum [Merged]

> Username: HDembinski  
> Created at: 2022-02-08 16:10:17 UTC  
> Updated at: 2025-09-01 16:47:53 UTC  
> Merged at: 2022-02-10 09:36:08 UTC  
> Closed at: 2022-02-10 09:36:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/351  

Closes #345   
  
This adds `operator/=` for `weighted_sum`, which is automatically picked up by `histogram::operator/=` and `histogram::operator/` if it is implemented in the accumulator. The variance of the result is computed under the assumption that both histograms contain uncorrelated data.  
  
This will not give the correct answer for efficiencies, where the numerator is a subset of the denominator. We will implement special efficiency accumulators (see #178) for this task.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2022-02-08 16:11:23 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-1032793989  

@henryiii I cannot select you as reviewer, but let me know if you have any thoughts on this.

---

## Comment 2

> Username: henryiii  
> Created_at: 2022-02-08 16:19:27 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-1032802758  

Seems reasonable and looks good. You can only add "members" of a repo as reviews, but that's fine, tagging me as you did actually is a little better about notifying me than adding me as a reviewer anyway. This will be an easy addition to boost-histogram. "special efficiency accumulators" will be a bit more work to add to boost-histogram, but sounds very useful.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2022-02-08 16:42:41 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-1032827963  

> You can only add "members" of a repo as reviews  
  
Not sure why you felt the need to explain that.

---

## Comment 4

> Username: henryiii  
> Created_at: 2022-02-08 16:46:59 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-1032832450  

Just because it's a GitHub limitation that I find slightly annoying - there are occasionally perfectly valid reasons to request someone who is not a member, like if you implement support for some library and you want the library's author to review it. In this case, though, tagging is ideal anyway.  
  
And you know it but it is useful for completeness for anyone reading this in the future.

---

## Comment 5

> Username: henryiii  
> Created_at: 2022-02-11 21:40:45 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-1036647306  

I'll sync up boost-histogram and add this as soon as it's in master.

---

## Comment 6

> Username: henryiii  
> Created_at: 2022-02-11 21:41:04 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-1036647496  

(Ideally ping me when that happens! :) )

---

## Comment 7

> Username: tomeichlersmith  
> Created_at: 2025-08-28 16:16:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-3234134592  

@henryiii to me, it seems like division of the weighted accumulator is present in boostorg/histogram master[^1] but I am still seeing the error in the latest boost_histogram:  
  
test file:  
```python  
import numpy as np  
import boost_histogram as bh  
  
def make_and_fill(w):  
    h = bh.Histogram(bh.axis.Regular(bins = 10, start = 0, stop = 1), storage = bh.storage.Weight())  
    h.fill(np.arange(0,1,0.1), weight = w)  
    return h  
  
def main():  
    print(bh.__version__)  
    h1 = make_and_fill(10*[1])  
    h2 = make_and_fill(10*[2])  
    h1/h2  
  
if __name__ == "__main__":  
    main()  
```  
result:  
```  
1.6.1  
Traceback (most recent call last):  
  File "/home/tom/code/ldmx/weighted-hist-div/main.py", line 16, in <module>  
    main()  
  File "/home/tom/code/ldmx/weighted-hist-div/main.py", line 13, in main  
    h1/h2  
    ~~^~~  
  File "/home/tom/code/ldmx/weighted-hist-div/.venv/lib/python3.12/site-packages/boost_histogram/histogram.py", line 550, in __truediv__  
    return result._compute_inplace_op("__itruediv__", other)  
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/home/tom/code/ldmx/weighted-hist-div/.venv/lib/python3.12/site-packages/boost_histogram/histogram.py", line 570, in _compute_inplace_op  
    getattr(self._hist, name)(other._hist)  
    ^^^^^^^^^^^^^^^^^^^^^^^^^  
AttributeError: 'boost_histogram._core.hist.any_weight' object has no attribute '__itruediv__'  
```  
  
[^1]: https://github.com/boostorg/histogram/commits/master/?after=6eaea7fead85b5e00c9a4c4d51d7b8fe335a8782+34

---

## Comment 8

> Username: henryiii  
> Created_at: 2025-08-28 16:55:21 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-3234249964  

Ping me after Saturday and I’ll look into it!

---

## Comment 9

> Username: tomeichlersmith  
> Created_at: 2025-09-01 16:47:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/351#issuecomment-3242912804  

Ping!

---
