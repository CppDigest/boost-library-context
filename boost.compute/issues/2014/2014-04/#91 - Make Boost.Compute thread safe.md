# #91 - Make Boost.Compute thread safe [Closed]

> Username: kylelutz  
> Created at: 2014-04-19 17:50:27 UTC  
> Updated at: 2014-09-30 04:53:02 UTC  
> Closed at: 2014-09-30 04:53:02 UTC  
> Url: https://github.com/boostorg/compute/issues/91  

Currently, Boost.Compute maintains some global/shared-state (like `program_cache`) which is not protected with any synchronization mechanisms and thus cannot be used concurrently from multiple threads.  
  
Boost.Compute should support being compiled in a mode (e.g. `BOOST_COMPUTE_THREAD_SAFE`) which allows multiple threads to call algorithms on separate `context`'s/`command_queue`'s simultaneously without interfering with each other.

---

## Comment 1

> Username: nickgildea  
> Created at: 2014-05-14 14:19:55 UTC  
> Url: https://github.com/boostorg/compute/issues/91#issuecomment-43086141  

I've been using Boost.Compute in a multi-threaded set up (originally adapted from a plain OpenCL API implementation): a single shared device, context and program with a thread-local cl_command_queue which is wrapped by a compute::command_queue object to be used, and compute::kernels are created as needed.  
  
Is this is a supported set up? Or does it just happen to work with Boost.Compute (e.g. am I just being lucky?). The fact that the same basic set up worked with OpenCL makes me think that it is OK -- both the OpenCL and Boost.Compute versions run in to bother when the queue is shared between threads.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-05-14 17:40:41 UTC  
> Url: https://github.com/boostorg/compute/issues/91#issuecomment-43112825  

The core OpenCL wrapper classes (e.g. `context`, `buffer`) offer the same thread-safety guarantees as the underlying OpenCL implementation so what you're doing should be safe.  
  
This bug is concerned with protecting the shared-state within Boost.Compute which is used for some of the higher-level functionality (e.g. program caching).

---

## Comment 3

> Username: nickgildea  
> Created at: 2014-05-14 19:33:19 UTC  
> Url: https://github.com/boostorg/compute/issues/91#issuecomment-43126658  

Ok, good to know. I'll be careful when making use of higher-level features.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-09-28 20:13:14 UTC  
> Url: https://github.com/boostorg/compute/issues/91#issuecomment-57098332  

Fixed in PR #269
