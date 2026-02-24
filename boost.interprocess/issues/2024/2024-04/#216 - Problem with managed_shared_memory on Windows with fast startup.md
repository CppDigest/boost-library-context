# #216 - Problem with managed_shared_memory on Windows with fast startup [Closed]

> Username: Trigve  
> Created at: 2024-04-05 11:38:51 UTC  
> Updated at: 2024-04-16 13:06:49 UTC  
> Closed at: 2024-04-16 12:44:43 UTC  
> Url: https://github.com/boostorg/interprocess/issues/216  

I'm using boost 1.80.0, x86, MSVC 2019 C++20 mode on Windows 10.  
  
I've a service process (1), that is using boost.interprocess `managed_shared_memory` for storing some global data using this code:  
```  
boost::interprocess::managed_shared_memory{boost::interprocess::create_only,  
	shm_name.c_str(),  
	4 * 1024  
}  
```  
The service is started during the windows start. Then another process (2) is reading this shared memory data using this code:  
```  
boost::interprocess::managed_shared_memory{boost::interprocess::open_only,  
	shm_name.c_str()  
}  
```  
The problem is, that when the Windows is shut down/started using "fast startup" mode, then when the process (2) is started, it cannot find the shared memory by the given name. Because on Windows the `managed_shared_memory` is emulated using files (AFAIK), when I look at the given location on file system where the shared memory is "stored", it's name doesn't correspond to the one, that is set during creation. It looks like the fast startup is somehow messing with it.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-04-16 09:53:45 UTC  
> Url: https://github.com/boostorg/interprocess/issues/216#issuecomment-2058696868  

I don't think there is a bug here. Shared memory should not be visible between startups (fast or otherwise). If you want data to persist, use memory mapped files (managed_mapped_file) . In fact, quite a bit effort was put so that shared memory from previous boot is not visible in the current boot.

---

## Comment 2

> Username: Trigve  
> Created at: 2024-04-16 12:44:43 UTC  
> Updated at: 2024-04-16 13:06:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/216#issuecomment-2059005909  

Thank you for the reply, I see now. I didn't find anything regarding the "Shared memory should not be visible between startups (fast or otherwise)" in the documentation so was under impression it is bug.
