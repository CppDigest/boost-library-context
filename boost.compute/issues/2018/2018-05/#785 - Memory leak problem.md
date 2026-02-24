# #785 - Memory leak problem [Open]

> Username: antibes0415  
> Created at: 2018-05-31 09:05:24 UTC  
> Updated at: 2018-06-06 06:09:20 UTC  
> Url: https://github.com/boostorg/compute/issues/785  

Hi. I use the most recent develop branch. Here's my problem description:  
I'm a developer of a software. After adding some codes of boost.compute, the software would crash (program stopped working) after exiting. Actually, all boost.compute objects should be destructed after use so that there shouldn't be crash problem after exiting the whole software. The software uses other 3D librarys like vtk to display 3D mesh. When exiting, it has a memory release mechanism to release GPU memory. So I think the problem may be repetitive release of GPU memory.  And there could really still be some memory leak problem.  
After trying some tests, I found that the the exit crash happens when the following codes are used:  
  
cl::Buffer *m_dev_sampleKeys;  
boost::compute::buffer cbSampleKeys((*m_dev_sampleKeys)());  
  boost::compute::buffer_iterator<cl_ulong> iter =  
    boost::compute::make_buffer_iterator<cl_ulong>(cbSampleKeys, 0);  
  boost::compute::sort(  
    iter,  
    iter + m_nbSamples,  
    m_compute_queue  
  );  
  
I have to note that all these boost.compute codes are run in a class object, which will be destructed immediately after use. So in normal case, it should not affect the proccess in software exiting.   
  
So I guess there really may be some problems in buffer or buffer_iterator or sorting. Could you check them?

---

## Comment 1

> Username: Ulfgard  
> Created at: 2018-05-31 09:11:43 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-393467282  

Could it be that this is #746 which got fixed a few days ago?

---

## Comment 2

> Username: antibes0415  
> Created at: 2018-05-31 09:24:39 UTC  
> Updated at: 2018-05-31 09:27:39 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-393470971  

Hi. I'm on develop branch and I've checked it's most recent. The problem does not  apper when just running my console application. Maybe windows would automatically collect memory leaks and garbages so there's no error in console.

---

## Comment 3

> Username: antibes0415  
> Created at: 2018-06-01 03:28:52 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-393747729  

Hi. I suppose it's some static object in boost.compute causing the crash. Static objects release memory in the last, which may cause some problem. In my case, I pass all context, queue with existed OpenCL objects (e.g. cl_context). I found that when I use boost::compute::sort (sort like several million numbers), the software would crash when exiting. Here is my program debug interface when crashing:  
![2018-06-01 11_18_36-d3dvideo debugging - microsoft visual studio administrator](https://user-images.githubusercontent.com/18564849/40819517-2058a1d0-658e-11e8-9d2d-0edd794de316.png)  
  
![2018-06-01 11_28_02-d3dvideo debugging - microsoft visual studio administrator](https://user-images.githubusercontent.com/18564849/40819684-e9194a52-658e-11e8-9bcb-519fc73458ca.png)

---

## Comment 4

> Username: antibes0415  
> Created at: 2018-06-05 06:24:12 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-394595520  

Hi. I add this line in the destructor of my Class and my software stops crashing after exiting.  
`  boost::compute::program_cache::get_global_cache(m_compute_context).get()->clear();`  
  
in which m_compute_context is set by:  
`cl::Context m_context = cl::Context(CL_DEVICE_TYPE_GPU, ...);`  
`boost::compute::context m_compute_context = boost::compute::context(m_context());`  
  
I'll keep watching.

---

## Comment 5

> Username: Ulfgard  
> Created at: 2018-06-05 08:42:28 UTC  
> Updated at: 2018-06-05 08:44:29 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-394630124  

I think the issue is the following:  
  
1. you create the context and tell boost compute about it.  
2. you create kernels, those are cached in the global cache  
3. you destroy your context, which frees all resources  
4. at program exit, the cache is cleared, this leads to a double free/corruption.  
  
//Edit could you try not creating your own context but using the context provided by boost::compute? or is this not possible in your application?

---

## Comment 6

> Username: antibes0415  
> Created at: 2018-06-05 09:01:00 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-394635552  

I have to create my own context because I write some complex kernels, and I need to use boost.compute and viennacl. So I need to pass the wrapper of my cl_context, cl_device and cl_mem etc. to boost.compute and viennacl.

---

## Comment 7

> Username: Ulfgard  
> Created at: 2018-06-05 09:08:13 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-394637820  

this was just to test whether this fixes the problem (so that we know that this is the culprit).   
  
you could query device, context and queue using boost::compute::system instead of providing your own.   
https://www.boost.org/doc/libs/1_62_0/boost/compute/system.hpp  
```  
cl_context& context = boost::compute::system::default_context().get();  
cl_device_id& device = boost::compute::system::default_device().get();  
command_queue& device = boost::compute::system::default_queue().get();  
```

---

## Comment 8

> Username: antibes0415  
> Created at: 2018-06-06 06:09:19 UTC  
> Url: https://github.com/boostorg/compute/issues/785#issuecomment-394951358  

Thanks.  
Actually, I use OpenCL 1.2 C++ wrapper. I can pass the cl_context to the cl::Context, but it would automatically destruct the context (release twice) and causes the crash in the end of the program. Don't know why. I suppose it should retain the context (counter + 1). I have commented the line   
`boost::compute::program_cache::get_global_cache(m_compute_context).get()->clear(); `  
in my class destructor. So Maybe it's difficult for me to test the cl_context  got from boost::compute::system::default_context().get().  
My code looks like this:  
  
```  
  cl::Context m_context;  
  std::vector<cl::Device> m_devices;  
  cl::CommandQueue m_queue;  
  boost::compute::device m_compute_device;  
  boost::compute::context m_compute_context;  
  boost::compute::command_queue m_compute_queue;  
  
bool PoissonReconGPU::initializeOpenCL() {  
  
  m_compute_context = boost::compute::system::default_context();  
  m_compute_device = boost::compute::system::default_device();  
  m_compute_queue = boost::compute::system::default_queue();  
  
  m_context = cl::Context(m_compute_context.get());  
  m_devices.push_back(cl::Device(m_compute_device.get()));  
  m_queue = cl::CommandQueue(m_compute_queue.get());  
...  
}  
```
