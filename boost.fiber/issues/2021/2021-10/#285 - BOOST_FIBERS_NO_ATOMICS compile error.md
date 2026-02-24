# #285 - BOOST_FIBERS_NO_ATOMICS compile error [Open]

> Username: karopawil  
> Created at: 2021-10-11 13:35:59 UTC  
> Updated at: 2021-10-11 14:00:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/285  

Hi,  
  
I am trying to compile with BOOST_FIBERS_NO_ATOMICS flag and getting compile errors like:  
  
/home/precitec/boost-build/boost_1_72_0/boost/fiber/context.hpp:395:25: error: request for member ‘fetch_add’ in ‘ctx->boost::fibers::context::use_count_’, which is of non-class type ‘std::size_t’ {aka ‘long unsigned int’}  
  395 |         ctx->use_count_.fetch_add( 1, std::memory_order_relaxed);  
  
Is this flag currently supported?  
  
Regards,  
Naveen

---

## Comment 1

> Username: olk  
> Created at: 2021-10-11 13:56:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/285#issuecomment-940055056  

seams to be a bug

---

## Comment 2

> Username: karopawil  
> Created at: 2021-10-11 14:00:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/285#issuecomment-940058230  

Thanks for the quick answer. Any plans to fix it?

---

## Comment 3

> Username: karopawil  
> Created at: 2021-10-11 14:00:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/285#issuecomment-940058564  

Or I could give it a shot if you gave me some hints.
