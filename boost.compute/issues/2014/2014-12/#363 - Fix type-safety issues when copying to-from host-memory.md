# #363 - Fix type-safety issues when copying to/from host-memory [Closed]

> Username: kylelutz  
> Created at: 2014-12-29 18:22:40 UTC  
> Updated at: 2016-06-01 04:27:09 UTC  
> Closed at: 2016-06-01 04:27:05 UTC  
> Url: https://github.com/boostorg/compute/issues/363  

There are cases where Boost.Compute blindly copies raw bytes of memory from the device to the host which are then interpreted wrong (e.g. 4-byte `float` on the device to 8-byte `double` on the host yields garbage values). Boost.Compute should automatically convert the values to the proper type when copying.  
  
Example from the Boost.Compute review:  
  
```  
compute::device device = compute::system::default_device();  
compute::context context(device);  
compute::command_queue queue(context, device);  
  
// generate random data on the host - type is float  
std::vector<float> host_vector(10000);  
std::generate(host_vector.begin(), host_vector.end(), rand);  
  
// create a vector on the device  
compute::vector<float> device_vector(host_vector.size(), context);  
  
// transfer data from the host to the device  
compute::copy(host_vector.begin(), host_vector.end(), device_vector.begin(), queue);  
  
double reduction_result = 0.0; // result is of type double  
compute::reduce(device_vector.begin(), device_vector.end(), &reduction_result, queue);  
std::cout << "result: " << reduction_result<< std::endl;  
```  
  
Another example:  
  
```  
// generate random data on the host - type is float  
std::vector<float> host_vector(10000);  
std::generate(host_vector.begin(), host_vector.end(), rand);  
  
// create a vector on the device - type is double  
compute::vector<double> device_vector(host_vector.size(), context);  
  
// transfer data from the host to the device  
compute::copy(host_vector.begin(), host_vector.end(), device_vector.begin(), queue);  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-05-17 09:18:39 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-219663663  

Currently, I'm working on copying data from host to the device and I have some questions.  
  
``` cpp  
template<class InputIterator, class OutputIterator>  
inline OutputIterator copy(InputIterator first,  
                           InputIterator last,  
                           OutputIterator result,  
                           command_queue &queue = system::default_queue())  
{  
    (...)  
}  
```  
  
I have 3 implementations:   
1. Copying data `[first; last)` on host to `std::vector<typename OutputIterator::value_type>` and then just performing `queue.enqueue_write_buffer(..)` to the `result`.  
2. Mapping `result` into host using `queue.enqueue_map_buffer(...)`, performing `std;:copy` (since `result` is mapped to host) and at the end unmaping `result` ([code](https://gist.github.com/haahh/5605c1057f6f0282437c777dd33ea24f)).  
3. Doing this (or something like this, I'm not sure if it works with structures):  
  
``` cpp  
boost::compute::mapped_view<int> mapped_host(host_vector.data(), host_vector.size(), context);  
boost::compute::transform(mapped_host.begin(),  
                          mapped_host.end(),  
                          device_vector.begin(),  
                          boost::compute::identity<typename OutputIterator::value_type>(),  
                          queue);  
```  
  
Which method is the best one depends on the size of the input `[first; last)` and the type of used device.   
  
GPU (input size and the fastest implementation):  
  
```  
0 - 0.5 MB:   2.  
0.5 - 50 MB:  1. (10% - 40% faster, never slower)  
50 - ... :    3. (up to 4x faster compared to 1.)  
```  
  
CPU (input size and the fastest implementation):  
  
```  
0 - 128 MB:  2.   
128 - ... :  3. (up to 3x faster compared to 2.; we use all cores)  
```  
- Should we have all of them and have some parameters to control which method is used?   
- Should those 3 versions of `copy_to_device` algorithm be in the public API so an advanced user can easily decide which he wants to use? Maybe in the `experimental` namespace? (On the other hand, an advanced user can go to `detail::`...)  
- Should we have public unsafe version of `copy` algorithm that ignores types mismatch, so user can copy whatever he wants (for exemple `int2` buffer to `int4` buffer etc.)?

---

## Comment 2

> Username: kylelutz  
> Created at: 2016-05-18 03:26:44 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-219915982  

Yeah, given the differences in performance for the various data sizes, I think it makes sense to have implementations for all three techniques and then select between them based on input size (which could even be auto-tuned for the particular hardware).  
  
And, I'd start by keeping them all under the `detail::` namespace, and we could potentially migrate them out later. In the future it would be nice to allow advanced users to have more control over what implementation we select for each algorithm (e.g. select radix sort or merge sort). But for now I think heuristics and tuning is the best approach which should work well for a majority of users.  
  
As for providing an unsafe version, I'd have to see a compelling use-case. For now I think it's easy enough to make a `buffer_iterator` with the desired type, even if the buffer it's pointing to contains different data (e.g. copy a buffer of floats into a buffer of float2s). Otherwise they can just call down to `command_queue::enqueue_write_buffer()` to perform "unsafe" copies between types (similar to just `memcpy()`'ing bytes in C++ rather than using `std::copy()`).  
  
Anyway, let me know what you think. And, awesome work! Thanks for providing all this detail!

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-05-18 12:29:49 UTC  
> Updated at: 2016-05-19 21:28:53 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-220011368  

TBH, I agree with you on all three things.    
  
There's an issue with structures as we can't easily cast/convert them. We can do two things:  
- Ignore it. Users will just get an OpenCL error during runtime. I think that will also be a common behaviour in other algorithms in similar circumstances.  
- In `boost::compute::copy` when `InputIterator` and `OutputIterator` value_types mismatch require both value_types to be fundamental OpenCL types.

---

## Comment 4

> Username: kylelutz  
> Created at: 2016-05-20 23:12:53 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-220739666  

Yeah, right now I think we will just blindly copy the bytes in the struct and assume they are interpreted the same on both the host and device. We could potentially do something smarter for structs which have been adapted with [`BOOST_COMPUTE_ADAPT_STRUCT`](http://www.boost.org/doc/libs/master/libs/compute/doc/html/BOOST_COMPUTE_ADAPT_STRUCT.html).

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-05-21 13:27:23 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-220777737  

Should we consider the fact that device iterator may not be contiguous when coping to/from host (`strided_iterator`)?

---

## Comment 6

> Username: kylelutz  
> Created at: 2016-05-24 02:42:40 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-221152029  

Yeah, it would be nice if we also supported copying non-contiguous iterators (or even arbitrary iterators like `transform_iterator`). That would allow more algorithms (like `transform()`) to support copying their results to host iterators.

---

## Comment 7

> Username: jszuppe  
> Created at: 2016-05-24 20:49:33 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-221396926  

Well, it's possible to copy results to host using `mapped_view`. I was thinking about this in context of `strided_iterator` as it is a read/write iterator.  Anyway, I will not implement coping to/from host with non-contiguous device iterators in _this task_.  I have enough work with this :)   
  
I have it done in 80% - see https://github.com/haahh/compute/commits/dev_typesafe_copy. I just need to add full support for `svm_ptr<>` and that requires adding support for it in `meta_kernel`. I'm planing to implement similarly to  `device_ptr<>` with its `device_ptr_index_expr<>` (so there will be a `svm_ptr_index_expr`). What do you think?

---

## Comment 8

> Username: jszuppe  
> Created at: 2016-05-25 20:44:10 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-221701462  

After trying to code this I think that `svm_ptr` need to be rethink/redesign or we need to have something like `svm_iterator` or `svm_buffer`. Anyway, we need to have sth that keeps pointer returned by `clSVMAlloc` and an index. `svm_ptr` contains just a simple pointer so when I want to make a `meta_kernel` method that would "register" `svm_ptr`s as kernel arguments (`std::string get_buffer_identifier(const svm_ptr &svm_ptr)`) I do not have a good way to check if two `svm_ptr`s comes from the same shared memory buffer. It's may be task for times after the BSoC ;)  
  
I've decided to try solving problem with `svm_ptr` differently by create a buffer with shared memory pointer as `host_ptr` argument and with `CL_MEM_USE_HOST_PTR` set, after that I should be able to just use a `buffer_iterator`. However, I'm not sure how it'll perform.

---

## Comment 9

> Username: jszuppe  
> Created at: 2016-05-25 22:38:26 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-221728811  

On the other hand, maybe for now it's not so bad that it is possible that to pointers passed to kernel are basically pointers to the same svm buffer pointing to different values. It may not happen so often or even don't happen at all... Ah, I need to rethink it once again ;-)

---

## Comment 10

> Username: kylelutz  
> Created at: 2016-05-26 05:16:06 UTC  
> Updated at: 2016-05-26 05:17:23 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-221779712  

Yeah, I like the approach with `mapped_view` (or mapped buffer), that should work well as long as the output range on the host is one contiguous chunk of memory. For other approaches you could do something similar to how we handle coping from non-contiguous host iterators, that is first copy to a single vector, and then copy from there to the device.

---

## Comment 11

> Username: kylelutz  
> Created at: 2016-06-01 04:27:03 UTC  
> Url: https://github.com/boostorg/compute/issues/363#issuecomment-222889464  

Fixed in PR #616
