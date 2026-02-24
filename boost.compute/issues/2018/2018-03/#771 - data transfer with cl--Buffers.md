# #771 - data transfer with cl::Buffers [Closed]

> Username: antibes0415  
> Created at: 2018-03-21 04:50:28 UTC  
> Updated at: 2018-03-24 20:24:02 UTC  
> Closed at: 2018-03-24 20:24:02 UTC  
> Url: https://github.com/boostorg/compute/issues/771  

Hi, I read the examples and found that data are transfered from host to device. But can I directly transfer a cl::Buffer object to the boost::compute::vector (device to device )or from compute::vector to cl::Buffer ? I think this is really important for high efficiency and interoperability with OpenCL C++ wrapper codes.

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-03-21 23:35:12 UTC  
> Url: https://github.com/boostorg/compute/issues/771#issuecomment-375130262  

`vector` -> `buffer`:  
You can get underlying `boost::compute::buffer` (which is a wrapper for OpenCL buffer object) from `boost::compute::vector` object using its `get_buffer()` member function (if you don't want to copy). You can also copy existing vector into created `boost::compute::buffer` using `boost::compute::copy` algorithm (you just need to create `buffer_iterator` using `buffer` object). `boost::compute::buffer` can be created from OpenCL buffer object (`cl_mem`).  
  
`buffer` -> `vector`  
You can't create vector from existing buffer so that buffer becomes vector's underlying buffer. You can copy existing buffer into existing vector using `boost::compute::copy` , or create new `boost::compute::vector` object using constructor which takes range `[first, last)`.

---

## Comment 2

> Username: antibes0415  
> Created at: 2018-03-22 02:56:35 UTC  
> Url: https://github.com/boostorg/compute/issues/771#issuecomment-375162914  

Hi. Thanks. Could you please explain a bit more about converting compute::vector to cl::buffer ?  
  
Right now I first do the following workflow:  
cl::buffer -> compute::buffer -> compute::vector -> sort compute::vector  
codes are:  
```  
cl::Buffer m_dev_sampleKeys = cl::Buffer(m_context, CL_MEM_READ_WRITE, m_nbSamples * sizeof(cl_long));  
// create data...  
boost::compute::buffer bSampleKeys(m_dev_sampleKeys()); // create from cl_mem  
boost::compute::vector<cl_long> vSampleKeys(boost::compute::make_buffer_iterator<cl_long>(bSampleKeys, 0),boost::compute::make_buffer_iterator<cl_long>(bSampleKeys, m_nbSamples), m_compute_queue);  
boost::compute::sort(vSampleKeys.begin(), vSampleKeys.end(), m_compute_queue);  
```  
  
Now how could I transfer the sorted compute::vector (vSampleKeys) data back to this cl::buffer (m_dev_sampleKeys)? My goal is not to use the host memory.  
  
I tried using boost::compute::copy  to copy vSampleKeys buffer to bSampleKeys. But I don't know how to finally copy to m_dev_sampleKeys.

---

## Comment 3

> Username: antibes0415  
> Created at: 2018-03-22 04:39:12 UTC  
> Url: https://github.com/boostorg/compute/issues/771#issuecomment-375177533  

By the way,  does boost::compute::buffer(cl_mem) always do a copy ? Could it be a reference?

---

## Comment 4

> Username: jszuppe  
> Created at: 2018-03-22 09:59:12 UTC  
> Updated at: 2018-03-22 10:00:32 UTC  
> Url: https://github.com/boostorg/compute/issues/771#issuecomment-375240858  

> Now how could I transfer the sorted compute::vector (vSampleKeys) data back to this cl::buffer (m_dev_sampleKeys)? My goal is not to use the host memory.  
  
I don't know why you want to use `cl::Buffer` and C++ wrapper from `cl.hpp` when you can just use Boost.Compute and don't waste any performance on copying. Anyway, to copy back to `m_dev_sampleKeys`, you just need to do this:  
```  
boost::compute::copy(  
  vSampleKeys.begin(),  
  vSampleKeys.end(),   
  boost::compute::make_buffer_iterator<cl_long>(bSampleKeys, 0),  
  m_compute_queue  
);  
```  
  
> By the way, does boost::compute::buffer(cl_mem) always do a copy ? Could it be a reference?  
  
`boost::compute::buffer(cl_mem)` constructor does not copy `cl_mem` object, it just wraps it. That means that when you copy to `bSampleKeys` you copy to `m_dev_sampleKeys`.

---

## Comment 5

> Username: jszuppe  
> Created at: 2018-03-22 10:01:18 UTC  
> Url: https://github.com/boostorg/compute/issues/771#issuecomment-375241457  

btw. You don't have to create `boost::compute::vector` to use Boost.Compute algorithms, you can just do:  
```cpp  
boost::compute::sort(  
  boost::compute::make_buffer_iterator<cl_long>(bSampleKeys, 0),  
  boost::compute::make_buffer_iterator<cl_long>(bSampleKeys, m_nbSamples),  
  m_compute_queue  
);  
```

---

## Comment 6

> Username: antibes0415  
> Created at: 2018-03-22 10:26:46 UTC  
> Url: https://github.com/boostorg/compute/issues/771#issuecomment-375248997  

Hi. You help me a lot. Thanks!  
And my apologies. I'm just a newbie in OpenCL and the original idea is that I want to use a library for something like sort, reduction, scan and count. Beyond that, I thought I would use the OpenCL C++ wrapper. I found that the document of boost.compute is not very detailed and I'm worried I could not make good use of it. The most important part may be the custom kernels. For my program, I need to write lots custom kernels. And I'm not clear how to use boost.compute to write kernels and set NDrange and run them... btw, I'm not clear the efficiency of running kernels compared with OpenCL.
