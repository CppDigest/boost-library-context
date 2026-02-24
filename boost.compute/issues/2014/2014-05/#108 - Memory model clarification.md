# #108 - Memory model clarification [Closed]

> Username: nanikos  
> Created at: 2014-05-08 21:22:54 UTC  
> Updated at: 2014-06-25 04:18:29 UTC  
> Closed at: 2014-06-25 04:18:29 UTC  
> Url: https://github.com/boostorg/compute/issues/108  

This is more of a clarification issue.  When data is copied to the device using compute::copy, what region of memory is it copied to?  Global memory, local memory or private memory?  This has important implications for speed of execution.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-09 00:35:36 UTC  
> Url: https://github.com/boostorg/compute/issues/108#issuecomment-42622760  

Calling `copy()` from the host to the device will either copy to global or constant memory (depending on the destination buffer). There is no way to directly write to local or private memory from the host API (those types of transfers are done by work-items in a kernel on the device). So for the host->device case, `copy()` just ends up calling `clEnqueueWriteBuffer()` to actually perform the copy operation.
