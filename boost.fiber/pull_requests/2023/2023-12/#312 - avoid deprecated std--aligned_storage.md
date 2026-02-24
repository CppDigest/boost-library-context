# #312 avoid deprecated std::aligned_storage [Merged]

> Username: Hailios  
> Created at: 2023-12-12 17:54:07 UTC  
> Updated at: 2023-12-24 07:12:05 UTC  
> Merged at: 2023-12-24 07:11:59 UTC  
> Closed at: 2023-12-24 07:11:59 UTC  
> Url: https://github.com/boostorg/fiber/pull/312  

this is in response to the deprecation warning noted in the mailing list.  
  
I think this patch will get rid of the warning, and also give smaller objects on some compilers.   
see https://gcc.gnu.org/bugzilla/show_bug.cgi?id=61458 for details of std::aligned_storage size.  
  
"  
[build] /usr/include/boost/fiber/future/detail/shared_state.hpp:160:19:  
warning: 'aligned_storage<4, 4>' is deprecated [-Wdeprecated-declarations]  
[build]   160 |     typename std::aligned_storage< sizeof( R), alignof(  
R) >::type  storage_{};  
[build]       |                   ^  
[build] /usr/include/boost/fiber/future/promise.hpp:28:22: note: in  
instantiation of template class  
'boost::fibers::detail::shared_state<int>' requested here  
[build]    28 |     typedef typename shared_state< R >::ptr_type   ptr_type;  
"

---

## Comment 1

> Username: olk  
> Created_at: 2023-12-24 07:12:04 UTC  
> Url: https://github.com/boostorg/fiber/pull/312#issuecomment-1868449780  

ty

---
