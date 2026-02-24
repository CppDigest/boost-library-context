# #70 - Improve inner_product performance [Closed]

> Username: roshanrags  
> Created at: 2014-03-19 00:18:39 UTC  
> Updated at: 2014-04-11 05:52:19 UTC  
> Closed at: 2014-04-11 05:52:19 UTC  
> Url: https://github.com/boostorg/compute/issues/70  

inner_product has to be improved a lot for it to even compare with STL. Currently, it takes 20x time compared to the STL version.  
  
PR #69 makes the implementation more native. I could get a performance gain of about 10x.  
  
But, it is still 2x slower than the STL version. I am trying out a vector-based implementation to make use of SIMD while multiplying/adding. Any other ideas?

---

## Comment 1

> Username: ddemidov  
> Created at: 2014-03-19 03:15:03 UTC  
> Url: https://github.com/boostorg/compute/issues/70#issuecomment-38013894  

I think `PERF_N` (1024) is too small to provide an adequate performance comparison for the inner product. For the vectors this small the performance is dominated by OpenCL overheads. [Here](http://arxiv.org/abs/1212.6326) the boost from OpenCL/CUDA was only apparent starting from vectors longer than 10e5-10e6.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-03-19 05:50:08 UTC  
> Updated at: 2014-03-19 05:52:18 UTC  
> Url: https://github.com/boostorg/compute/issues/70#issuecomment-38019974  

Yeah, performance shouldn't be judged with such small data sizes. @roshanr95 At what data sizes did you see the performance differences?  
  
I'll look into bumping up the default number. In my usual testing I usually set `PERF_N` to `16000000` (by specifying it on the command line). Also @roshanr95, I assume you found the `perf.py` script which will run the algorithm with a range of data sizes and also display a graph if you run it with the `--plot-time` or `--plot-rate` arguments.

---

## Comment 3

> Username: roshanrags  
> Created at: 2014-03-19 09:11:31 UTC  
> Url: https://github.com/boostorg/compute/issues/70#issuecomment-38030065  

Yeah, I used the python script. The performance differences are for size of 33554432 elements.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-04-11 05:52:19 UTC  
> Url: https://github.com/boostorg/compute/issues/70#issuecomment-40172828  

Fixed. See issue #73 and the discussion in issue #69.
