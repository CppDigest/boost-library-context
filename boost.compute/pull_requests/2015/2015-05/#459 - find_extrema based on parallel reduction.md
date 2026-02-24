# #459 find_extrema based on parallel reduction [Merged]

> Username: jszuppe  
> Created at: 2015-05-19 20:32:39 UTC  
> Updated at: 2015-05-25 07:43:49 UTC  
> Merged at: 2015-05-21 14:57:30 UTC  
> Closed at: 2015-05-21 14:57:30 UTC  
> Url: https://github.com/boostorg/compute/pull/459  

This adds `find_extrema_reduce` function and change `find_extrema` function to use it if there is enough local memory on device to perform an efficient parallel reduction. If there is not, `find_extema_with_atomics` is used (just like it was).  
  
`find_extrema_reduce` and `find_extema_with_atomics` get the same time results in `perf_max_element` benchmark. For vectors with 134217728 elements the difference is visible.   
  
master branch:  
  
```  
=== max_element with stl ===  
size,time (ms)  
2,0.000231  
4,0.000260  
8,0.000286  
16,0.000299  
32,0.000312  
64,0.000364  
128,0.000421  
256,0.000519  
512,0.000779  
1024,0.001274  
2048,0.002192  
4096,0.004097  
8192,0.007887  
16384,0.015551  
32768,0.030970  
65536,0.061633  
131072,0.123388  
262144,0.251553  
524288,0.506069  
1048576,1.010500  
2097152,2.298420  
4194304,4.540600  
8388608,9.029470  
16777216,18.179500  
33554432,36.464900  
=== max_element with compute ===  
size,time (ms)  
2,2.938670  
4,2.953160  
8,2.982040  
16,3.020810  
32,2.960240  
64,3.023210  
128,3.006550  
256,3.052890  
512,3.005860  
1024,2.995740  
2048,3.060540  
4096,2.994000  
8192,3.076960  
16384,3.008020  
32768,3.046970  
65536,3.040360  
131072,3.092490  
262144,3.138090  
524288,3.187000  
1048576,2.986230  
2097152,3.074310  
4194304,3.280390  
8388608,3.608900  
16777216,4.278090  
33554432,5.508340  
---   
67108864, 7.90407 ms  
134217728, 13.3829 ms  
  
=== max_element with bolt ===  
size,time (ms)  
2,0.239094  
4,0.242006  
8,0.242288  
16,0.243363  
32,0.242257  
64,0.243461  
128,0.243329  
256,0.241237  
512,0.413800  
1024,0.520461  
2048,0.729220  
4096,1.151180  
8192,1.964450  
16384,3.618160  
32768,6.824630  
65536,11.551800  
131072,22.584200  
262144,28.026900  
524288,28.072100  
1048576,27.904700  
2097152,28.255900  
4194304,28.788000  
8388608,26.075500  
16777216,25.414200  
33554432,28.391500  
```  
  
pr_improving_find_extrema branch:  
  
```  
=== max_element with compute ===  
size,time (ms)  
2,2.951420  
4,3.024390  
8,2.964650  
16,3.017970  
32,3.006210  
64,3.144770  
128,3.117730  
256,3.126830  
512,3.064650  
1024,3.069900  
2048,3.055710  
4096,3.098720  
8192,3.127860  
16384,3.130310  
32768,3.098050  
65536,3.114520  
131072,3.190490  
262144,3.240010  
524288,3.252840  
1048576,3.347880  
2097152,3.471760  
4194304,3.582500  
8388608,3.625560  
16777216,4.242310  
33554432,5.249320  
---   
67108864, 7.20043 ms  
134217728, 11.2549 ms  
  
```  
  
However, when worst case scenario is used, i.e. vector contains elements from 0 to `vector.size() - 1`, performance of  `find_extema_with_atomics` decreases.   
  
``` cpp  
int global = 0;  
int next_int()  
{  
    return global++;  
}  
```  
  
When function `next_int()` is used to fill vector instead of `rand_int()`, performance of `find_extema_reduce` remains the same, but performance of  `find_extema_with_atomics` decreases:  
  
```  
2097152,5.000910  
4194304,6.930040  
8388608,10.957300  
16777216,18.637500  
33554432,34.256900  
```  
  
Another argument for merging this PR is fact that `find_extema_reduce` work for OpenCL 1.0, when current `find_extema_with_atomics`  does not (I have to say that I don't no why it does not work).

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-05-20 03:25:03 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30669015  

For these sort of calculations, I think you may be able to use the `calculate_work_size()` function in [`<boost/compute/detail/work_size.hpp>`](https://github.com/boostorg/compute/blob/master/include/boost/compute/detail/work_size.hpp).

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-05-20 03:25:33 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30669027  

Just for debugging?

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-05-20 03:30:59 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30669209  

For unordered queues it is usually preferred to use `event`-based synchronization instead of calling `clFinish()` (which will stop and wait for all kernels to complete, not just those for this algorithm). In this case, you would store the `event` returned from `enqueue_nd_range_kernel()` and pass that as a part of the `wait_list` to the next operation on the queue.  
  
Do you have devices which support out-of-order execution to test with? For the most part, Boost.Compute algorithms assume in-order execution. It may be best to leave this out for now until we can add full out-of-order queue support to Boost.Compute. Let me know what you think.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-05-20 03:32:29 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30669240  

This should probably be read from the `parameter_cache`. For an example, see the [`reduce_on_gpu()`](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp) algorithm implementation. This allows us to tune the parameter for different compute devices.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2015-05-20 03:35:21 UTC  
> Url: https://github.com/boostorg/compute/pull/459#issuecomment-103744712  

Looks good, left a few inline comments. Benchmark numbers look great!

---

## Comment 6

> Username: jszuppe  
> Created_at: 2015-05-20 06:34:56 UTC  
> Url: https://github.com/boostorg/compute/pull/459#issuecomment-103778661  

Thanks for all your comments! That's why I like code reviews and four-eyes principle.

---

## Comment 7

> Username: jszuppe  
> Created_at: 2015-05-20 14:34:27 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30708003  

Yes, you're right. I know `event`-based synchronization should be used with out-of-order queue. I just thought "oh, it will not work with out-of-order queue" and I can't get `event`, so I did what I did :)  
If we assume in-order, it's unnecessary perform `finish()` and indeed it affects other kernels. I will remove this part.   
  
Having out-of-order queue would be nice, but sadly AMD OpenCL drives does not implement this feature.

---

## Comment 8

> Username: jszuppe  
> Created_at: 2015-05-20 14:34:51 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30708067  

Yeah. I missed it.

---

## Comment 9

> Username: jszuppe  
> Created_at: 2015-05-20 15:05:01 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30711777  

OK. In that case I think I will add `work_group_size`  and `work_groups_per_compute_unit` parameters, because they both influence performance of the `find_extrema_reduce`.  
  
Q: There are two naming styles/conventions "OpenCL-based" and "CUDA-based". I understand that both styles can be used alternatively in Boost.Compute? I ask because I tends to use OpenCL naming style in code (like "work_item" instead of "thread", "work_group" instead of "block", "compute unit" instead of "multiprocessor" etc.) and I've seen that in mentioned `reduce_on_gpu()` you named parameters "vpt" and "tpb". Should I follow this style when I name parameters for `find_extrema_reduce`?

---

## Comment 10

> Username: jszuppe  
> Created_at: 2015-05-20 15:05:21 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30711819  

I don't know how `calculate_work_size()` can help in this situation. It calculate global work size which I don't need at this point.  
  
I just need to check if work group size isn't greater than maximal work group size for used device, then calculate number of work groups - it is equal to `compute_units_no * 64` (64 does not represent how many values will be reduced by one work item, it depends on vector size), or when vector is small, it is `static_cast<size_t>(std::ceil(float(count) / work_group_size))`.

---

## Comment 11

> Username: pavanky  
> Created_at: 2015-05-20 15:54:22 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30717519  

From my experiments, the performance is better if the strides are equal to local size instead of global size. We saw up 10-20% improvement (atleast for NVIDIA GPUs) in ArrayFire when we made this change. This would require the idx to be computed a bit differently. You can look at the reference code here: https://github.com/arrayfire/arrayfire/blob/devel/src/backend/opencl/kernel/ireduce_first.cl#L11

---

## Comment 12

> Username: jszuppe  
> Created_at: 2015-05-20 16:27:46 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30720865  

Interesting! I'll try to test it. Any idea why there is this 10-20% improvement?

---

## Comment 13

> Username: pavanky  
> Created_at: 2015-05-20 17:03:03 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30724130  

I am not entirely sure. My guess would be fewer cache misses. If the data is not aligned to the cache boundary, each read will need to get two cache lines per global stride. If using a local stride, the remaining data from the second cache line will be useful for the next iteration.

---

## Comment 14

> Username: jszuppe  
> Created_at: 2015-05-20 19:11:24 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30737877  

I'm getting the same time results. CodeXL shows the same low cache hit percentage, almost 0.  Maybe I did not understand correctly, but this is how I've changed idx calculation (this is generated kernel code cause it is easier to read):  
  
``` cpp  
const uint id = (get_group_id(0) * get_local_size(0) * vpwi) + get_local_id(0);  
uint m = id + (get_local_size(0) * vpwi);  
m = count < m ? count : m;  
if(id >= count) {  
    return;  
}  
uint idx = id;  
int acc;  
uint acc_idx = idx;  
acc = _buf0[idx]; // first[k.var<uint_>("idx")]  
idx += get_local_size(0);  
bool compare_result;  
while( idx < m){  
    int next = _buf0[idx]; // first[k.var<uint_>("idx")]  
    compare_result = acc > next;  
    acc = compare_result ? acc : next;  
    acc_idx = compare_result ? acc_idx : idx;  
    idx += get_local_size(0);  
}  
...  
```  
  
Where `vpwi` is values per work item. I have to add that in this kernel number of work groups per compute unit is constant, `vpwi` isn't.

---

## Comment 15

> Username: pavanky  
> Created_at: 2015-05-20 19:16:39 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30738413  

The code looks fine. Let me do a quick test on an nvidia gpu.

---

## Comment 16

> Username: pavanky  
> Created_at: 2015-05-20 19:20:19 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30738762  

Is there any chance you can push the alternative version to a different branch so I won't have to reimplement it ?

---

## Comment 17

> Username: jszuppe  
> Created_at: 2015-05-20 19:26:49 UTC  
> Updated_at: 2015-05-21 11:28:13 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30739370  

Sure, I was just about to do it.  
  
Pushed: https://github.com/haahh/compute/tree/dev_improving_find_extrema_perf_local_size_stride

---

## Comment 18

> Username: jszuppe  
> Created_at: 2015-05-21 11:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/459#issuecomment-104241782  

I removed out-of-order queue check and printf pragma. I added `parameter_cache` for work group size and number of work groups per compute unit.

---

## Comment 19

> Username: kylelutz  
> Created_at: 2015-05-21 14:57:27 UTC  
> Url: https://github.com/boostorg/compute/pull/459#issuecomment-104308830  

Thanks! Merged!

---

## Comment 20

> Username: jszuppe  
> Created_at: 2015-05-21 15:10:22 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30811762  

@pavanky And how does it work? Have you noticed any increase in the performance?

---

## Comment 21

> Username: jszuppe  
> Created_at: 2015-05-21 15:11:36 UTC  
> Url: https://github.com/boostorg/compute/pull/459#issuecomment-104314088  

Great!  
  
I think after GSoC'15 (or earlier if I will have time) I will add support for custom comparison function for `find_extrema` (therefore for `max_element()` and `min_element()`). I'll create issue for this.  
  
@pavanky Thanks for your input!

---

## Comment 22

> Username: pavanky  
> Created_at: 2015-05-21 15:12:45 UTC  
> Url: https://github.com/boostorg/compute/pull/459#discussion_r30812002  

I have had trouble running the max_element example on my machine. It keeps segfaulting. I don't think it is an issue with your code. It may be a bug in compute itself. I can only test it after the issue is fixed.

---
