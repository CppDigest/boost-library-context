# #768 - meta_kernel::compile is very slow, even for cached source [Open]

> Username: Ulfgard  
> Created at: 2018-02-26 10:40:18 UTC  
> Updated at: 2021-07-08 12:01:00 UTC  
> Url: https://github.com/boostorg/compute/issues/768  

Hi,  
  
I am currently benchmarking the overhead of boost::compute. My test-program is computing nothing big and the equivalent cpu program finishes in no measurable runtime. The OpenCL version takes several seconds, so i decided to check with valgrind-callgrind where this time is lost. My program utilizes 11 different kernels and the runtime is the Intel OpenCL cpu implementation. I am using callgrind with O1 optimizations.  
  
71% of total run-time is spent in 8149 calls to meta_kernel::compile.  
Inside, we have over all 8149 calls:  
total-runtime% function  
43.21% clCreateKernel  
14.5% compute::detail::sha1::sha1  
8.7% program::build_with_source  
  
As a sanity check:   
1.6% meta_kernel::source  
1.2% clEnqueueNDRangeKernel  
  
so, roughly 58% of total running time is spent on figuring out whether the program is already build and creating the kernel afterwards. Compiling and caching works fine, we have exactly 11 calls to build_with_source.  
  
The problem is that compute assumes that kernels are throw-away objects. This is unfortunately not the case. e.g. on NVIDIA, clCreateKernel is reported to have an overhead of roughly 1ms. My runtime seems to be in the same order. The programs built with meta_kernel have only one kernel function, so one easy workaround would be to cache the kernel together with the program.  
  
Also, sha1 is terribly slow. The hash function should not take an order of magnitude longer than creating the source in the first place.

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-02-27 20:20:11 UTC  
> Url: https://github.com/boostorg/compute/issues/768#issuecomment-369012758  

Thanks for that research/information. I guess it will be very helpful for anyone that would decide to donate his time into refactoring Boost.Compute `meta_kernel` and its compilation process.   
  
> 58% of total running time is spent on figuring out whether the program is already build and creating the kernel afterwards  
  
What about operations on `std::string`s? Do you think they take much time?  
  
> The problem is that compute assumes that kernels are throw-away objects. This is unfortunately not the case. e.g. on NVIDIA, clCreateKernel is reported to have an overhead of roughly 1ms.  
> The programs built with meta_kernel have only one kernel function, so one easy workaround would be to cache the kernel together with the program.  
  
That's possible. However, kernels can be only cached online.  
  
> Also, sha1 is terribly slow. The hash function should not take an order of magnitude longer than creating the source in the first place.  
  
I guess `sha1`  is the easiest part to replace.

---

## Comment 2

> Username: Ulfgard  
> Created at: 2018-03-01 15:01:39 UTC  
> Updated at: 2018-03-01 15:04:27 UTC  
> Url: https://github.com/boostorg/compute/issues/768#issuecomment-369619153  

I have not done more research on the other operations. I think string processing takes most of the remaining time. The current issue should be fixable by a quite straight forward rewrite of the program_cache  
  
I think, instead of storing programs, we can store a structure like:  
  
```  
struct program_entry{  
    program p;  
    std::map<std::string, kernel> kernels;  
};  
```  
  
and than we have a new method that also stores kernels:  
  
```  
    kernel get_or_build_kernel(const std::string &key, const std::string &options, const std::string& kernel_name, const std::string &source, const context &context)  
    {  
        //build program entry if it does not exist  
        if(!m_cache.contains(std::make_pair(key,options))){  
            program_entry entry;  
            entry.p = program::build_with_source(source, context, options);  
  
            m_cache.insert(std::make_pair(key, options), entry);  
        }  
        //get pointer to the existing entry so that we can change it  
        //Note: find does currently not exist but is straight-forward to add.  
        program_entry* entry = m_cache.find(std::make_pair(key, options));  
        auto& kernels = entry->kernels;  
        //return kernel if it exists  
        auto kernel_pos = kernels.find(kernel_name);  
        if(kernel_pos != kernels.end()){  
            return *kernel_pos;  
        }  
          
        kernels[kernel_name] = entry->p.create_kernel(kernel_name);  
        return kernels[kernel_name];  
    }  
```  
  
and meta_kernel calls this instead get_or_build. This should not lead to problems with thread safety as there is already one cache per thread. Am I missing something?  
  
//edit small changes in untested and uncompiled code sketch :)

---

## Comment 3

> Username: Ulfgard  
> Created at: 2018-05-29 09:56:12 UTC  
> Url: https://github.com/boostorg/compute/issues/768#issuecomment-392720960  

I am trying to get a round this issue. I have tuned my meta_kernel so that each expression is always mapping to the same source(i.e. all constant are transformed to kernel variables). Could i then use static thread_local kernels like this?  
  
`static thread_local compute::kernel k = create_my_kernel(...);`  
  
Where create_my_kernel creates the source with meta_kernel, compiles the program and returns the kernel. Would this fix the caching issue as every thread always has a complete list of all kernels? Could I run into problems with artifical driver limitations, e.g. maximum number of programs upported by the OpenCL implementation? I have not found any hint towards that limitation.

---

## Comment 4

> Username: Scorpkolev  
> Created at: 2021-07-08 09:11:06 UTC  
> Updated at: 2021-07-08 09:11:43 UTC  
> Url: https://github.com/boostorg/compute/issues/768#issuecomment-876272087  

>   
>   
> I have not done more research on the other operations. I think string processing takes most of the remaining time. The current issue should be fixable by a quite straight forward rewrite of the program_cache  
>   
> I think, instead of storing programs, we can store a structure like:  
>   
> ```  
> struct program_entry{  
>     program p;  
>     std::map<std::string, kernel> kernels;  
> };  
> ```  
>   
> and than we have a new method that also stores kernels:  
>   
> ```  
>     kernel get_or_build_kernel(const std::string &key, const std::string &options, const std::string& kernel_name, const std::string &source, const context &context)  
>     {  
>         //build program entry if it does not exist  
>         if(!m_cache.contains(std::make_pair(key,options))){  
>             program_entry entry;  
>             entry.p = program::build_with_source(source, context, options);  
>   
>             m_cache.insert(std::make_pair(key, options), entry);  
>         }  
>         //get pointer to the existing entry so that we can change it  
>         //Note: find does currently not exist but is straight-forward to add.  
>         program_entry* entry = m_cache.find(std::make_pair(key, options));  
>         auto& kernels = entry->kernels;  
>         //return kernel if it exists  
>         auto kernel_pos = kernels.find(kernel_name);  
>         if(kernel_pos != kernels.end()){  
>             return *kernel_pos;  
>         }  
>           
>         kernels[kernel_name] = entry->p.create_kernel(kernel_name);  
>         return kernels[kernel_name];  
>     }  
> ```  
>   
> and meta_kernel calls this instead get_or_build. This should not lead to problems with thread safety as there is already one cache per thread. Am I missing something?  
>   
> //edit small changes in untested and uncompiled code sketch :)  
  
Good day! Could you give an example of using the "get_or_build_kernel" function and explain 2 questions  
- Why do you need a variable "key"  
- What type has a variable "m_cache"

---

## Comment 5

> Username: Ulfgard  
> Created at: 2021-07-08 10:14:05 UTC  
> Updated at: 2021-07-08 10:19:52 UTC  
> Url: https://github.com/boostorg/compute/issues/768#issuecomment-876315043  

I don't understand your question. m_cache is a member of class program_cache in boost.compute. key is part of its interface.  
When i remember correctly, key is in most cases the sha1 hash of the source used to quickly look up the cached program.  
  
https://github.com/boostorg/compute/blob/36c89134d4013b2e5e45bc55656a18bd6141995a/include/boost/compute/utility/program_cache.hpp

---

## Comment 6

> Username: Scorpkolev  
> Created at: 2021-07-08 11:51:17 UTC  
> Url: https://github.com/boostorg/compute/issues/768#issuecomment-876373845  

>   
>   
> I don't understand your question. m_cache is a member of class program_cache in boost.compute. key is part of its interface.  
> When i remember correctly, key is in most cases the sha1 hash of the source used to quickly look up the cached program.  
>   
> https://github.com/boostorg/compute/blob/36c89134d4013b2e5e45bc55656a18bd6141995a/include/boost/compute/utility/program_cache.hpp  
  
Thanks for the quick response!   
I thought this function was yours, not from the boost lib.

---

## Comment 7

> Username: Ulfgard  
> Created at: 2021-07-08 12:01:00 UTC  
> Url: https://github.com/boostorg/compute/issues/768#issuecomment-876379293  

it is a new function but mimics the currently existing interface, e.g. compare to get_or_build. the only change i propose is that the kernels should be constructed and cached as well and i outlined a way to do that along the existing code base.
