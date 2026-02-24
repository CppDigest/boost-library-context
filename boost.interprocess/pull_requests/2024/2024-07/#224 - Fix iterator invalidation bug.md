# #224 Fix iterator invalidation bug. [Merged]

> Username: adalisk-emikhaylov  
> Created at: 2024-07-23 11:35:11 UTC  
> Updated at: 2024-08-07 20:46:32 UTC  
> Merged at: 2024-08-07 20:46:17 UTC  
> Closed at: 2024-08-07 20:46:17 UTC  
> Url: https://github.com/boostorg/interprocess/pull/224  

This fixes https://github.com/boostorg/interprocess/issues/192 and https://github.com/boostorg/interprocess/issues/210 (which seem to describe the same bug).  
  
Here's the code I'm testing this on. I'm using VS 2022 (though some coworkers with the same compiler can't reproduce, I'm not entirely sure why)  
```cpp  
#define BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION 1 // This is important, doesn't crash otherwise.  
  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/managed_windows_shared_memory.hpp>  
#include <boost/interprocess/smart_ptr/unique_ptr.hpp>  
#include <iostream>  
  
int main()  
{  
    std::cout << "Before\n";  
    {  
        namespace bip = boost::interprocess;  
        // Destructor of `x` crashes.  
        auto x = bip::managed_windows_shared_memory( bip::open_or_create, "blah", bip::mapped_region::get_page_size() );  
    }  
    std::cout << "After\n";  
}  
```

---

## Comment 1

> Username: adalisk-emikhaylov  
> Created_at: 2024-08-06 08:38:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/224#issuecomment-2270708507  

@igaztanaga A friendly ping.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2024-08-07 20:46:30 UTC  
> Url: https://github.com/boostorg/interprocess/pull/224#issuecomment-2274319492  

Many thanks for the pull request!

---
