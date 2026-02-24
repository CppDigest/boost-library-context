# #106 - Passing a fixed_stack allocator to async/packaged_task doesn't compile on gcc 5.4 [Closed]

> Username: brandon-kohn  
> Created at: 2017-01-12 01:48:57 UTC  
> Updated at: 2017-01-12 08:13:59 UTC  
> Closed at: 2017-01-12 08:13:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/106  

I'm trying to rewrite the skynet test using async to compare with HPX's implementation. When I tried the following cases it fails to compile as it expects the stack allocator to conform to alloc_traits.h (i.e. define value_type etc.)  
  
>  
        for (std::uint64_t i = 0; i != div; ++i)  
        {  
            std::uint64_t sub_num = num + i * size;  
            boost::fibers::packaged_task<std::uint64_t(allocator_type&, std::uint64_t, std::uint64_t, std::uint64_t)> pt{std::allocator_arg, salloc, skynet };  
            boost::fibers::future<std::uint64_t> f{ pt.get_future() };  
            boost::fibers::fiber{ boost::fibers::launch::dispatch, std::allocator_arg, salloc,  
                std::move(pt), std::ref(salloc), sub_num, size, div }.detach();  
            results.push_back(f);  
            //results.push_back(boost::fibers::async(  
            //      boost::fibers::launch::dispatch  
            //    , std::allocator_arg, salloc  
            //    , skynet  
            //    , std::ref(salloc), sub_num, size, div));  
        }  
  
Here's the error message:  
  
>/usr/include/c++/5/bits/alloc_traits.h:88:43: error: no type named ‘value_type’ in ‘class boost::context::basic_fixedsize_stack<boost::context::stack_traits>’  
       typedef typename _Alloc::value_type value_type;  
  
Apologies if it's something silly I've missed.

---

## Comment 1

> Username: olk  
> Created at: 2017-01-12 08:00:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/106#issuecomment-272100266  

You are right - the problem is that in async.hpp:81 a packaged_task is constructed using a stack-allocator as argument - that is false (should be the default ctor for packaged_task).  
Seams that an additional overload of async() is required.

---

## Comment 2

> Username: olk  
> Created at: 2017-01-12 08:13:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/106#issuecomment-272100531  

fixed
