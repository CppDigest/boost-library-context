# #825 - Wrong type for CL_DEVICE_GLOBAL_MEM_CACHELINE_SIZE [Closed]

> Username: WKarel  
> Created at: 2019-02-15 20:17:32 UTC  
> Updated at: 2019-02-16 09:41:15 UTC  
> Closed at: 2019-02-16 09:41:15 UTC  
> Url: https://github.com/boostorg/compute/issues/825  

device::get_info<CL_DEVICE_GLOBAL_MEM_CACHELINE_SIZE> is specialized for cl_ulong instead of for cl_uint

---

## Comment 1

> Username: jszuppe  
> Created at: 2019-02-16 09:41:15 UTC  
> Url: https://github.com/boostorg/compute/issues/825#issuecomment-464325440  

Fixed in #826
