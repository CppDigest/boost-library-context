# #268 - How do i madvise the mapped_region for managed_shared_memory? [Open]

> Username: rahuldutta90  
> Created at: 2025-08-05 01:12:00 UTC  
> Updated at: 2025-08-25 21:09:27 UTC  
> Url: https://github.com/boostorg/interprocess/issues/268  

For performance reasons we want to preallocate memory in the shared memory by calling madvise(MADV_POPULATE_WRITE). This generally has significant benefit in performance during the bootstrap phase as compared to allocate memory when the memory is accessed. However there are 2 reasons why we cannot do this today:  
  
1. First the managed_shared_memory does not expose the public apis of managed_open_or_create_impl so we are unable to access to api get_mapped_region  
2. Even we would have access to the mapped_region, the advise api supports only selected options (enum advice_types) and MADV_POPULATE_WRITE is not part of it.  
  
  
Do you plan to support this?  
  
I guess the only viable alternative is I separately create the shared_memory and madvise it before initializing using boost's shared_memory something like this:  
  
fd= shm_open("BoostShmem", O_CREATE)  
ptr = mmap64(  
      NULL, fileSize, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);  
madvise(ptr, fileSize, MADV_POPULATE_WRITE);  
boost::interprocess::managed_shared_memory shared_memory(boost::interprocess::create_only,  
      "BoostShmem", fileSize);

---

## Comment 1

> Username: rahuldutta90  
> Created at: 2025-08-19 02:53:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/268#issuecomment-3199037064  

Hi any input here?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-08-19 16:12:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/268#issuecomment-3201371004  

There is no plan to add madvice support for managed types, but this could change if there is support on the community. I guess supporting madvise for the whole memory mapped range would be enough.  
  
On the other hand, the available madvise-like flags are modelled after POSIX flags because Boost.Interprocess hopes for minimally portable abstractions, and MADV_POPULATE_XXX options seem quite Linux-specific. There a lot of different, system-specific flags around (BSD, Linux, etc.), which I don't think are compatible enough, although I might be wrong. Would "advice_willneed" (translated to MADV_WILLNEED for linux) be acceptable for your use-case? Which is the main advantages of those MADV_POPULATE_XXX specific options?

---

## Comment 3

> Username: rahuldutta90  
> Created at: 2025-08-20 03:33:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/268#issuecomment-3204065435  

Thanks @igaztanaga   
  
>  I guess supporting madvise for the whole memory mapped range would be enough.  
  
Yes, this is what i am trying to do.  
  
> Would "advice_willneed" (translated to MADV_WILLNEED for linux) be acceptable for your use-case? Which is the main advantages of those MADV_POPULATE_XXX specific options?  
  
I need to check if that would suffice. MADV_POPULATE_XXX helps pre-allocating the underlying memory and also make it visible to userspace.  
  
However i do see a workaround. Looking through the code, i do see this api "get_address" (exposed here: https://github.com/boostorg/interprocess/blob/b11c6a1365266d64f66ec61554262ff4301c782f/include/boost/interprocess/detail/managed_memory_impl.hpp#L229). Based on high level look through the code, I think it does return the pointer that we have gotten from mmap of the shared memory. Is that a correct understanding? If yes, then we should be able to call madvise ourselves on the memory pointer that we get from get_address provided that is the memory pointer.  
  
This is my understanding:  
  
We get mmap address here: https://github.com/boostorg/interprocess/blob/b11c6a1365266d64f66ec61554262ff4301c782f/include/boost/interprocess/mapped_region.hpp#L711   
  
and we set the address from mapped_region here: https://github.com/boostorg/interprocess/blob/b11c6a1365266d64f66ec61554262ff4301c782f/include/boost/interprocess/detail/managed_open_or_create_impl.hpp#L367  
  
which finally sets the address here: https://github.com/boostorg/interprocess/blob/b11c6a1365266d64f66ec61554262ff4301c782f/include/boost/interprocess/detail/managed_memory_impl.hpp#L742 which is setting it here: https://github.com/boostorg/interprocess/blob/b11c6a1365266d64f66ec61554262ff4301c782f/include/boost/interprocess/detail/managed_memory_impl.hpp#L180 and being returned by the get_address.  
  
Is my understanding correct ehre? Can we safely assume the pointer we get from get_address is the mmap ed memory pointer?

---

## Comment 4

> Username: igaztanaga  
> Created at: 2025-08-25 21:09:27 UTC  
> Url: https://github.com/boostorg/interprocess/issues/268#issuecomment-3221753698  

Yes, the address returned by get_address is the base address of the first mapped memory page.
