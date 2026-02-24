# #271 Fix 64b segments from 32b code [Closed]

> Username: rp42  
> Created at: 2025-09-12 10:50:48 UTC  
> Updated at: 2025-11-12 10:52:10 UTC  
> Closed at: 2025-11-11 22:50:20 UTC  
> Url: https://github.com/boostorg/interprocess/pull/271  

Fix for code that shares boost::interprocess::basic_managed_shared_memory between a 64b and 32b process as described here: https://stackoverflow.com/a/63224381  
  
Prior code doesn't compile:  
detail\segment_manager_helper.hpp(391,14): error : no matching function for call to 'get_rounded_size'  
  
The template parameter type for ipcdetail::block_header<unsigned long long>::name_offset<> is 32b (std::size_t), whereas the other sizes are 64b (uint64_t). This causes the get_rounded_size() template to be rejected since both arguments are not of the same type.  
  
https://github.com/boostorg/interprocess/issues/270

---

## Comment 1

> Username: rp42  
> Created_at: 2025-11-11 10:05:50 UTC  
> Url: https://github.com/boostorg/interprocess/pull/271#issuecomment-3516003396  

Hi @igaztanaga, I've raised this small PR that fixes an issue I had with some code that accesses a shared memory segment from both 32b and 64b processes. I've not contributed to Boost before, please could you let me know how I should go about getting it merged. Thanks!

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2025-11-11 22:50:20 UTC  
> Url: https://github.com/boostorg/interprocess/pull/271#issuecomment-3519013443  

An alternative fix is already in the develop and master branch. Many thanks for the PR.

---

## Comment 3

> Username: rp42  
> Created_at: 2025-11-12 10:52:10 UTC  
> Url: https://github.com/boostorg/interprocess/pull/271#issuecomment-3521316799  

Hi @igaztanaga , I've tested with https://github.com/boostorg/interprocess/commit/8825137dedc27827d8e14f3c7b2959ad0dd5fc5c and this now fixes the issue here. Many thanks for this and for such a fantastic library!

---
