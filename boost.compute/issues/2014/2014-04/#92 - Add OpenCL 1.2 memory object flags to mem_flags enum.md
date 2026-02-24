# #92 - Add OpenCL 1.2 memory object flags to mem_flags enum [Closed]

> Username: kylelutz  
> Created at: 2014-04-19 17:53:12 UTC  
> Updated at: 2014-07-12 21:54:29 UTC  
> Closed at: 2014-07-12 21:54:29 UTC  
> Url: https://github.com/boostorg/compute/issues/92  

OpenCL 1.2 introduced some new memory flags (e.g. `  
CL_MEM_HOST_NO_ACCESS`) used when callig `clCreateBuffer()`. These should be added to the `memory_object::mem_flags` enum. Ensure they are protected with an `#ifdef CL_VERSION_1_2` so that older OpenCL versions still compile.  
  
See: https://www.khronos.org/registry/cl/sdk/1.2/docs/man/xhtml/clCreateBuffer.html

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-12 21:45:29 UTC  
> Url: https://github.com/boostorg/compute/issues/92#issuecomment-48824924  

Implemented in PR #194.
