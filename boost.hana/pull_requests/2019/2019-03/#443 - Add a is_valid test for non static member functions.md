# #443 Add a is_valid test for non static member functions [Merged]

> Username: sdebionne  
> Created at: 2019-03-15 09:23:25 UTC  
> Updated at: 2019-06-05 11:15:46 UTC  
> Merged at: 2019-05-10 18:13:38 UTC  
> Closed at: 2019-05-10 18:13:38 UTC  
> Url: https://github.com/boostorg/hana/pull/443  

I am not sure if this is actually supported. My experiment with MSVC is that the compiler ICE  when  
instantiating `auto boost::hana::type_detail::is_valid_impl<...>` but let see what the CI says.

---

## Comment 1

> Username: ldionne  
> Created_at: 2019-05-10 18:13:53 UTC  
> Url: https://github.com/boostorg/hana/pull/443#issuecomment-491383022  

Thanks for the added test coverage!

---
