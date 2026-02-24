# #646 - How to implement `maskedSelect` using boost.compute, in opencl? [Closed]

> Username: hughperkins  
> Created at: 2016-08-21 19:51:38 UTC  
> Updated at: 2016-09-04 13:03:23 UTC  
> Closed at: 2016-08-24 11:40:31 UTC  
> Url: https://github.com/boostorg/compute/issues/646  

Hi Karl,  
  
I need to port maskedSelect from CUDA to opencl.  Currently, the code uses thrust, ie https://github.com/torch/cutorch/blob/master/lib/THC/generic/THCTensorMasked.cu#L116 Thoughts on how to write a maskedSelect in boost.compute, using opencl?  
  
Hugh

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-08-21 19:56:44 UTC  
> Updated at: 2016-08-21 20:09:28 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241278057  

There is a `copy_if()` algorithm in Boost.Compute, and you can used `zip_iterator` as an input iterator to pass your mask and data, or you can modify `transform_if()` (used in `copy_if()`) so it matches your requirements.

---

## Comment 2

> Username: hughperkins  
> Created at: 2016-08-21 20:36:56 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241280470  

Ok.  CAn you flesh that out a bit please?  Is there a unit test that calls it for example, in opencl, that I could copy/paste?

---

## Comment 3

> Username: hughperkins  
> Created at: 2016-08-21 20:39:19 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241280610  

(for example, how can I pass in my existing cl context, queue, and so on ?)

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-08-21 21:14:14 UTC  
> Updated at: 2016-08-21 21:17:50 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241282490  

Ah, ok, so you use pure OpenCL and want to use Boost.Compute.  I will assume you have your OpenCL context, queue and buffer objects. In Boost.Compute you have iterators and wrapper classes for OpenCL objects.  
  
You can construct `boost::compute::command_queue` from `cl_command_queue`, see https://github.com/boostorg/compute/blob/master/include/boost/compute/command_queue.hpp#L101.  
  
You can also create `boost::compute::buffer` from OpenCL memory object, see https://github.com/boostorg/compute/blob/master/test/test_buffer.cpp#L55, and later make a buffer iterator from it, see https://github.com/boostorg/compute/blob/master/test/test_buffer_iterator.cpp.  
  
Let's ignore `copy_if()` and use `transform_if()` (https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/transform_if.hpp) to implementation). The idea behind `transform_if()` is simple, copy those elements of an input vector that returns true for a given predicate, and before copying them to the output vector apply a given function to them (and save the result, not the value itself). See https://github.com/boostorg/compute/blob/master/test/test_transform_if.cpp for examples.  
  
A `zip_iterator` is needed because you have a mask, not a predicate, and `transform_if()` accepts an input iterators, output iterator, a function and a predicate, so the simplest solution is to zip `buffer_iterator` for the mask and `buffer_iterator` for the input data together and in predicate check if mask value equals one. `zip_iterator` examples: https://github.com/boostorg/compute/blob/master/test/test_zip_iterator.cpp. Ultimately, it would look like this:  
  
``` cpp  
transform_if(  
  make_zip_iterator(  
    input_iterator_begin,  
    mask_iterator_begin  
  ),  
  make_zip_iterator(  
    input_iterator_end,  
    mask_iterator_end  
  ),  
  output_iterator_begin,  
  get<0>(), // function that return input value  
  lambda::get<1>(_1) == 1, // lambda function that checks if mask is 1  
  queue // command queue (boost::compute::command_queue object)  
);  
```  
  
Notice that `get<0>()` and `lambda::get<1>(_1)` are different objects, check https://github.com/boostorg/compute/blob/master/test/test_lambda.cpp#L344.

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-08-21 21:16:19 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241282592  

I hope that will help.

---

## Comment 6

> Username: hughperkins  
> Created at: 2016-08-21 21:17:48 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241282652  

Thanks!  And this will all run in OpenCL, on the gpu, in an approximately efficient manner?  
  
By the way, how do I fix: `compute-boost-1.61.0/include/boost/compute/config.hpp:14:28: fatal error: boost/config.hpp: No such file or directory` ?  Seems like config.hpp references itself? https://github.com/boostorg/compute/blob/master/include/boost/compute/config.hpp#L14

---

## Comment 7

> Username: hughperkins  
> Created at: 2016-08-21 21:22:24 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241282913  

Oh... boost.compute has a dependency on core boost?  
  
```  
// check for minimum required boost version  
#if BOOST_VERSION < 105400  
#error Boost.Compute requires Boost version 1.54 or later  
#endif  
```

---

## Comment 8

> Username: jszuppe  
> Created at: 2016-08-21 21:28:45 UTC  
> Updated at: 2016-08-21 21:45:13 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241283276  

> By the way, how do I fix: compute-boost-1.61.0/include/boost/compute/config.hpp:14:28: fatal error: boost/config.hpp: No such file or directory ? Seems like config.hpp references itself? https://github.com/boostorg/compute/blob/master/include/boost/compute/config.hpp#L14  
  
No, it references Boost Library config.  
  
> Thanks! And this will all run in OpenCL, on the gpu, in an approximately efficient manner?  
  
Yes, however, if your mask has only 0 and 1, you can make it faster: see here https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/transform_if.hpp#L51 we check the input vector against the predicate to count for how many values it returns true. You can make your own function (based on `transform_if()`) with small changes that would just accept iterators for an input and a mask, that immediately calculate result size using mask (also that way you don't really need to allocate output before calculating the size, which will save you same space). I can't have time to write it now in form of an example, but I can later if you need it.

---

## Comment 9

> Username: jszuppe  
> Created at: 2016-08-21 21:35:35 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241283651  

> Oh... boost.compute has a dependency on core boost?  
  
Yes, it requires Boost (also see http://www.boost.org/doc/libs/1_61_0/libs/compute/doc/html/boost_compute/getting_started.html#boost_compute.getting_started.configuration_macros).

---

## Comment 10

> Username: hughperkins  
> Created at: 2016-08-21 22:11:39 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241285519  

Ok, I've installed `boost-dev`, and created boost compute context and queue ok.  Then, it looks like I have to somehow convert my `cl_mem` object into a boost compute vector?  Looking at https://boostorg.github.io/compute/boost/compute/vector.html I dont see any way of constructing from a boost compute `cl_mem` object or from a boost compute `buffer` object?  
  
( What I have so far, as a prototype https://gist.github.com/hughperkins/a4d92ed96fae556a678923594b069159 )

---

## Comment 11

> Username: jszuppe  
> Created at: 2016-08-21 22:18:54 UTC  
> Updated at: 2016-08-21 22:20:23 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241285882  

That's true, you can't construct vector from a buffer. But you just need iterators, you can create iterators from a Boost.Compute buffer object. Like in here: https://github.com/boostorg/compute/blob/master/test/test_buffer_iterator.cpp#L43. You just need to wrap `cl_mem` in `boost::compute::buffer` and then you can create iterator with `make_buffer_iterator<>()` If I understand correctly you don't need a Boost.Compute vector since you already handling the allocation in a different way in cltorch, am I right?

---

## Comment 12

> Username: hughperkins  
> Created at: 2016-08-21 22:20:10 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241285947  

ah, buffer iterator looks like what I need.  Thanks! :-)

---

## Comment 13

> Username: jszuppe  
> Created at: 2016-08-21 22:23:39 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241286114  

;) ok. I'm glad I could help, I'm going to sleep right now, so if you have some questions I won't answer them until tomorrow 9 am CET.

---

## Comment 14

> Username: hughperkins  
> Created at: 2016-08-21 22:25:34 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241286211  

Ok, Thank you a lot Jacob :-)

---

## Comment 15

> Username: hughperkins  
> Created at: 2016-08-21 22:36:49 UTC  
> Updated at: 2016-08-21 22:37:12 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241286753  

yay, that works! :-)  Thank you Jacob :-)  https://gist.github.com/hughperkins/b215fad7d04321c86c29f8ed4db84ca5  
  
output:  
  
```  
3 4 5 1 2   
3 4 2 0 0   
```

---

## Comment 16

> Username: jszuppe  
> Created at: 2016-08-22 08:06:34 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241340910  

That's great!

---

## Comment 17

> Username: hughperkins  
> Created at: 2016-08-22 14:14:27 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241425926  

Hmmm, getting a seg fault:  
  
```  
#0  0x0000000000000000 in ?? ()  
#1  0x00002aaace606e37 in enqueue_fill_buffer (events=...,   
    size=<optimized out>, offset=<optimized out>,   
    pattern_size=<optimized out>, pattern=<optimized out>, buffer=...,   
    this=<optimized out>)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/command_queue.hpp:712  
#2  boost::compute::detail::dispatch_fill<boost::compute::buffer_iterator<unsigned int>, unsigned int> (first=..., count=258676,   
    value=value@entry=@0x7fffffffbdbc: 0, queue=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/fill.hpp:135  
#3  0x00002aaace6072f4 in fill<boost::compute::buffer_iterator<unsigned int>, unsigned int> (last=..., queue=..., value=@0x7fffffffbdbc: 0, first=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/fill.hpp:286  
#4  boost::compute::detail::scan_impl<boost::compute::buffer_iterator<unsigned int>, boost::compute::buffer_iterator<unsigned int>, unsigned int, boost::compute::plus<unsigned int> > (first=..., last=..., result=...,   
    exclusive=exclusive@entry=true, init=init@entry=0, op=..., queue=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/detail/scan_on_gpu.hpp:222  
#5  0x00002aaace607d92 in boost::compute::detail::dispatch_scan<boost::compute::buffer_iterator<unsigned int>, unsigned int, boost::compute::plus<unsigned int> > (first=..., last=..., result=..., exclusive=exclusive@entry=true,   
    init=init@entry=0, op=..., queue=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/detail/scan_on_gpu.hpp:303  
#6  0x00002aaace608498 in scan_on_gpu<boost::compute::buffer_iterator<unsigned int>, boost::compute::buffer_iterator<unsigned int>, unsigned int, boost::compute::plus<unsigned int> > (queue=..., op=..., init=0, exclusive=true, result=...,   
    last=..., first=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/detail/scan_on_gpu.hpp:324  
#7  scan<boost::compute::buffer_iterator<unsigned int>, boost::compute::buffer_iterator<unsigned int>, unsigned int, boost::compute::plus<unsigned int> > (  
    queue=..., op=..., init=0, exclusive=true, result=..., last=..., first=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/detail/scan.hpp:37  
#8  exclusive_scan<boost::compute::buffer_iterator<unsigned int>, boost::compute::buffer_iterator<unsigned int> > (queue=..., result=..., last=..., first=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/exclusive_scan.hpp:90  
```  
  
Code is here:  
https://github.com/hughperkins/cltorch/blob/master/src/lib/THClTensorMasked.cpp#L223-L254  
  
```  
  // ==== boost compute bit starts ========  
  
    EasyCL *cl = src->storage->cl;  
  
    boost::compute::context boost_context(*cl->context);  
    boost::compute::command_queue boost_queue(*cl->queue);  
  
    boost::compute::buffer boostData(*contigSrc->storage->wrapper->getDeviceArray());  
    boost::compute::buffer boostMask(*contigMask->storage->wrapper->getDeviceArray());  
    boost::compute::buffer boostOut(*tensor->storage->wrapper->getDeviceArray());  
  
    transform_if(  
      make_zip_iterator(  
        boost::make_tuple(  
        boost::compute::make_buffer_iterator<float>(boostData, 0),  
        boost::compute::make_buffer_iterator<float>(boostMask, 0)  
        )  
      ),  
      make_zip_iterator(  
        boost::make_tuple(  
        boost::compute::make_buffer_iterator<float>(boostData, THClTensor_nElement(state, mask)),  
        boost::compute::make_buffer_iterator<float>(boostMask, THClTensor_nElement(state, mask))  
        )  
      ),  
      boost::compute::make_buffer_iterator<float>(boostOut, 0),  
      boost::compute::get<0>(), // function that return input value  
      boost::compute::lambda::get<1>(boost::compute::_1) == 1, // lambda function that checks if mask is 1  
      boost_queue // command queue (boost::compute::command_queue object)  
    );  
    tensor->storage->wrapper->markDeviceDirty();  
  
// ==== boost compute bit ends ========  
```  
  
Thoughts?

---

## Comment 18

> Username: hughperkins  
> Created at: 2016-08-22 14:20:54 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241427992  

Turned on debug build:  
  
```  
#0  0x0000000000000000 in ?? ()  
#1  0x00002aaace6734b6 in boost::compute::command_queue::enqueue_fill_buffer (  
    this=0x7fffffffd860, buffer=..., pattern=0x7fffffffb81c, pattern_size=4,   
    offset=0, size=1034704, events=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/command_queue.hpp:712  
#2  0x00002aaace6960b3 in boost::compute::detail::dispatch_fill<boost::compute::buffer_iterator<unsigned int>, unsigned int> (first=..., count=258676,   
    value=@0x7fffffffb92c: 0, queue=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/fill.hpp:135  
#3  0x00002aaace6928ee in boost::compute::fill<boost::compute::buffer_iterator<unsigned int>, unsigned int> (first=..., last=..., value=@0x7fffffffb92c: 0,   
    queue=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/fill.hpp:286  
#4  0x00002aaace68e827 in boost::compute::detail::scan_impl<boost::compute::buffer_iterator<unsigned int>, boost::compute::buffer_iterator<unsigned int>, unsigned int, boost::compute::plus<unsigned int> > (first=..., last=..., result=...,   
    exclusive=true, init=0, op=..., queue=...)  
    at /data/torch-cl/opencl/cltorch/src/lib/../boost-headers-lite/boost/compute/algorithm/detail/scan_on_gpu.hpp:222  
```

---

## Comment 19

> Username: jszuppe  
> Created at: 2016-08-22 14:41:46 UTC  
> Updated at: 2016-08-22 14:41:58 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241434863  

I don't know. I'll try to recreate it on my PC and look into.

---

## Comment 20

> Username: hughperkins  
> Created at: 2016-08-22 14:45:55 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241436260  

Ok.  it looks like mask and input tensor have 66220894 elements.  mask has 33110448 1s in it.

---

## Comment 21

> Username: hughperkins  
> Created at: 2016-08-22 14:47:32 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241436805  

I guess in all fairness I should probably create the simplest example that fails first on the whole.

---

## Comment 22

> Username: jszuppe  
> Created at: 2016-08-22 15:16:08 UTC  
> Updated at: 2016-08-22 15:24:17 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241446382  

Well, I've tried recreating this bug and I can't force a seg fault even with huge input buffers (I've used `boost::compute::vector<float>`s).

---

## Comment 23

> Username: jszuppe  
> Created at: 2016-08-22 15:32:49 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-241451975  

My test case is modified `perf_saxpy.cpp`. I replaced `transform()` call from line 53 ([link](https://github.com/boostorg/compute/blob/master/perf/perf_saxpy.cpp#L53)) with:  
  
```  
transform_if(  
      make_zip_iterator(  
        boost::make_tuple(  
            x.begin(),  
            y.begin()  
        )  
      ),  
      make_zip_iterator(  
        boost::make_tuple(  
            x.end(),  
            y.end()  
        )  
      ),  
      result.begin(),  
      boost::compute::get<0>(), // function that return input value  
      boost::compute::lambda::get<1>(_1) != T(0), // it practically always return true  
      queue  
    );  
```  
  
You can run it this way `./perf/perf_saxpy 66220894`.

---

## Comment 24

> Username: jszuppe  
> Created at: 2016-08-24 09:33:34 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242008134  

Any progress?

---

## Comment 25

> Username: hughperkins  
> Created at: 2016-08-24 10:36:52 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242022280  

I got as far as creating a relatively simple script, that runs ok on my NVIDIA 940M, but crashes on a Titan X running over bfboost.  The obvious next step will be to run it on non-bfboost titan x, and see if its a bfboost bug, or something about Titan X.  The script is:  
  
```  
#include <iostream>  
#include <sstream>  
#include <stdexcept>  
using namespace std;  
  
// #define USE_EASYCL  
  
#ifdef USE_EASYCL  
#include "clew.h"  
#include "EasyCL.h"  
#else  
#include "CL/cl.h"  
#endif  
  
#include <boost/compute/core.hpp>  
#include <boost/compute/iterator/buffer_iterator.hpp>  
#include <boost/compute/algorithm/reverse.hpp>  
#include <boost/compute/algorithm/transform_if.hpp>  
#include <boost/compute/lambda.hpp>  
#include <boost/compute/iterator/zip_iterator.hpp>  
  
template<typename T>  
std::string toString(T val ) { // not terribly efficient, but works...  
   std::ostringstream myostringstream;  
   myostringstream << val;  
   return myostringstream.str();  
}  
  
void checkError( cl_int err ) {  
    if (err != CL_SUCCESS) {  
       throw std::runtime_error( "Error: " + toString(err) );  
    }  
}  
  
int main( int argc, char *argv[] ) {  
  
    cl_int err;    
  
  
#ifdef USE_EASYCL  
    EasyCL *cl = EasyCL::createForFirstGpuOtherwiseCpu();  
    cl_command_queue queue = *cl->queue;  
    cl_context ctx = *cl->context;  
#else  
    cl_device_id *device_ids;  
  
    cl_uint num_platforms;  
    cl_uint num_devices;  
  
    cl_platform_id platform_id;  
    cl_device_id device;  
  
    cl_context ctx;  
    cl_command_queue queue;  
    cl_program program;  
  
    checkError( clGetPlatformIDs(1, &platform_id, &num_platforms) );  
    checkError(  clGetDeviceIDs(platform_id, CL_DEVICE_TYPE_GPU, 1, &device, &num_devices) );  
    device_ids = new cl_device_id[num_devices];  
    checkError( clGetDeviceIDs(platform_id, CL_DEVICE_TYPE_GPU, num_devices, device_ids, &num_devices) );  
    device = device_ids[0];  
    ctx = clCreateContext(0, 1, &device, NULL, NULL, &err);  
    checkError(err);  
    queue = clCreateCommandQueue(ctx, device, 0, &err);  
    checkError(err);  
#endif  
  
     // const int N = 80;  
     // const int outN = 50;  
  
     const int N = 40000000;  
     const int outN = N >> 1;  
  
     // const int N = 66220894;  
     // const int outN = 33110448;  
  
     float *mask = new float[N];  
     float *data = new float[N];  
     float *out = new float[outN];  
     for( int i = 0; i < N; i++) {  
         mask[i] = 0;  
         data[i] = i;  
     }  
     for( int i = 0; i < outN; i++) {  
         mask[i] = 1;  
     }  
    // float mask[N] = {1,1,0,0,1};  
    // float data[N] = {3,4,5,1,2};  
    // float out[outN] = {0,0,0};  
    cl_mem bufMask = clCreateBuffer(ctx, CL_MEM_READ_ONLY, N * sizeof(*mask),  
                        NULL, &err);  
    cl_mem bufData = clCreateBuffer(ctx, CL_MEM_READ_ONLY, N * sizeof(*data),  
                        NULL, &err);  
    cl_mem bufOut = clCreateBuffer(ctx, CL_MEM_READ_WRITE, outN * sizeof(*out),  
                        NULL, &err);  
     err = clEnqueueWriteBuffer(queue, bufMask, CL_TRUE, 0,  
      N * sizeof(*mask), mask, 0, NULL, NULL);  
     err = clEnqueueWriteBuffer(queue, bufData, CL_TRUE, 0,  
      N * sizeof(*data), data, 0, NULL, NULL);  
     err = clEnqueueWriteBuffer(queue, bufOut, CL_TRUE, 0,  
      outN * sizeof(*data), out, 0, NULL, NULL);  
  
    boost::compute::context boost_context(ctx);  
    boost::compute::command_queue boost_queue(queue);  
  
    boost::compute::buffer boostData(bufData);  
    boost::compute::buffer boostMask(bufMask);  
    boost::compute::buffer boostOut(bufOut);  
  
    // // reverse the values in the buffer  
    // boost::compute::reverse(  
    //     boost::compute::make_buffer_iterator<float>(boostData, 0),  
    //     boost::compute::make_buffer_iterator<float>(boostData, 5),  
    //     boost_queue  
    // );  
  
    transform_if(  
      make_zip_iterator(  
        boost::make_tuple(  
        boost::compute::make_buffer_iterator<float>(boostData, 0),  
        boost::compute::make_buffer_iterator<float>(boostMask, 0)  
        )  
      ),  
      make_zip_iterator(  
        boost::make_tuple(  
        boost::compute::make_buffer_iterator<float>(boostData, N),  
        boost::compute::make_buffer_iterator<float>(boostMask, N)  
        )  
      ),  
      boost::compute::make_buffer_iterator<float>(boostOut, 0),  
      boost::compute::get<0>(), // function that return input value  
      boost::compute::lambda::get<1>(boost::compute::_1) == 1, // lambda function that checks if mask is 1  
      boost_queue // command queue (boost::compute::command_queue object)  
    );  
  
    checkError( clFinish( queue ) );  
    checkError( clEnqueueReadBuffer( queue, bufData, CL_TRUE, 0, sizeof(float) * N, data, 0, NULL, NULL) );      
    checkError( clEnqueueReadBuffer( queue, bufOut, CL_TRUE, 0, sizeof(float) * outN, out, 0, NULL, NULL) );      
    checkError( clFinish( queue ) );  
  
    // cout << "data:" << endl;  
    // for( int i = 0; i < N; i++ ) {  
    //    cout << data[i] << " ";  
    // }  
    // cout << endl;  
    // cout << "out:" << endl;  
    // for( int i = 0; i < outN; i++ ) {  
    //    cout << out[i] << " ";  
    // }  
    // cout << endl;  
  
    return 0;  
}  
```  
  
(I cant remember whether it only crashes if I define `USE_EASYCL` or not, I need to check this point.

---

## Comment 26

> Username: hughperkins  
> Created at: 2016-08-24 10:42:05 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242023270  

Ok. it runs ok on non-bfboot titan x, with or without easycl, either ok.  I'll sbmit a bug to bfboost.  Seems like therefore both boostcompute, and the new cltorch maskedselect, using your code above, are working in fact :-)

---

## Comment 27

> Username: jszuppe  
> Created at: 2016-08-24 10:55:24 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242025853  

> Ok. it runs ok on non-bfboot titan x, with or without easycl, either ok. I'll sbmit a bug to bfboost. Seems like therefore both boostcompute, and the new cltorch maskedselect, using your code above, are working in fact :-)  
  
Good to hear that! How about the performance?

---

## Comment 28

> Username: hughperkins  
> Created at: 2016-08-24 11:00:35 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242026846  

it's not in a performance-critical section of code, it just needs to work.  However, there are a few methods in cltorch that need exclusive scans and similar, so the presence of boost compute in cltorch now opens the way to porting those from the cuda torch in a straightforward fashion.

---

## Comment 29

> Username: hughperkins  
> Created at: 2016-08-24 11:01:04 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242026934  

(well, basiclaly, I havent measured the performance, I'm still in the process of logging bug report to bfboost...)

---

## Comment 30

> Username: hughperkins  
> Created at: 2016-08-24 11:30:43 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242032517  

Hi Jacob,  
  
So, I still have some work to go getting cltorch to work on neuralconvo https://github.com/hughperkins/distro-cl/issues/8#issuecomment-242028418 . But it looks like maskedSelect is either working, or very close to working, now :-)  
  
Thank you very much for having provided an implementation of maskedSelect using boost.compute.  It looks like this is going to work well, and looks very clean.  Thank you for having helped out with this Jacob, incredibly useful :-)

---

## Comment 31

> Username: jszuppe  
> Created at: 2016-08-24 11:35:52 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242033465  

I'm always happy to help when I can :) Good luck!

---

## Comment 32

> Username: hughperkins  
> Created at: 2016-08-25 12:51:15 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242374388  

Hi Jacob,  
  
Question: how to determine how big the output tensor should be, the one that will receive the masked select result?  
  
Current code uses some proprietary code to calculate the sum, but this is overflowing, in the sense that it exceeds the float precision (something like > 2^24), and thus the size is wrong by 2.  https://github.com/hughperkins/cltorch/blob/069074f74c68407c5499d1c4cbcb3305fadfcc96/src/lib/THClTensorMasked.cpp#L211  
  
Thoughts?

---

## Comment 33

> Username: jszuppe  
> Created at: 2016-08-25 13:01:46 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242377356  

Hmm.. If mask is just ones and zeroes you should be able to use 32bit  
integers and just count the ones (like it's done here  
https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/transform_if.hpp#L57),  
or you can reduce the mask and the result is your size. Am I right?  
  
25 sie 2016 14:51 "Hugh Perkins" notifications@github.com napisał(a):  
  
Hi Jacob,  
  
Question: how to determine how big the output tensor should be, the one  
that will receive the masked select result?  
  
Current code uses some proprietary code to calculate the sum, but this is  
overflowing, in the sense that it exceeds the float precision (something  
like > 2^24), and thus the size is wrong by 2. https://github.com/  
hughperkins/cltorch/blob/069074f74c68407c5499d1c4cbcb3305fadfcc96/src/lib/  
THClTensorMasked.cpp#L211  
  
Thoughts?  
  
—  
You are receiving this because you commented.  
  
Reply to this email directly, view it on GitHub  
https://github.com/boostorg/compute/issues/646#issuecomment-242374388,  
or mute  
the thread  
https://github.com/notifications/unsubscribe-auth/ACmCBHMlvzqQ9U3P33j3JoSHVkZlPr2kks5qjY_FgaJpZM4JpX1k  
.

---

## Comment 34

> Username: hughperkins  
> Created at: 2016-08-25 13:23:00 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242383793  

Nice! Thanks! :-)  That works very nicely :-)  https://github.com/hughperkins/cltorch/blob/43e163e25ec257b99b75b08bb0911f86856f46de/src/lib/THClTensorMasked.cpp#L247-L252

---

## Comment 35

> Username: jszuppe  
> Created at: 2016-08-25 13:34:37 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242387728  

Good :-), and since transform_if() already calculates the size of the output array you can copy body of it into your function and allocate your output array after the counting, thus saving a few milliseconds. Ofc only if you need them ;-)

---

## Comment 36

> Username: hughperkins  
> Created at: 2016-08-25 13:44:25 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242391229  

Ok.  I'm not sure how to do that at the moment, but I sense that I'm going to be using boost.compute more and more, and might probably understand how to do this at some point in the nearish future :-)  Thank you for all your help on this; really useful :-)

---

## Comment 37

> Username: jszuppe  
> Created at: 2016-08-25 13:53:15 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242394250  

No problem 👍 ttyl!

---

## Comment 38

> Username: hughperkins  
> Created at: 2016-08-27 13:28:20 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242917198  

(added your name to [Recent Changes](https://github.com/hughperkins/distro-cl#recent-changes)  Hope that's ok?)

---

## Comment 39

> Username: jszuppe  
> Created at: 2016-08-27 13:48:31 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-242918257  

Wow, that's more than ok! Thanks :)

---

## Comment 40

> Username: hughperkins  
> Created at: 2016-09-04 13:03:23 UTC  
> Url: https://github.com/boostorg/compute/issues/646#issuecomment-244602452  

(background info for anyone using bfboost: bfboost now supports the OpenCL methods needed to run this, and this runs ok on bfboost now, without segfaulting)
