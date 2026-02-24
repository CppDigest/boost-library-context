# #44 Fix -Wdeprecated-copy [Open]

> Username: georgthegreat  
> Created at: 2022-12-29 14:10:32 UTC  
> Updated at: 2024-02-13 22:51:37 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/44  

```  
boost/circular_buffer/include/boost/circular_buffer/details.hpp:263:15: error: definition of implicit copy constructor for 'iterator<boost::circular_buffer<bool>, boost::cb_details::const_traits<std::allocator<bool>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    iterator& operator=(const iterator&) = default;  
              ^  
```

---

## Comment 1

> Username: georgthegreat  
> Created_at: 2022-12-29 14:13:56 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/44#issuecomment-1367357341  

I think that compiler-generated assignment operator should work as intended.

---

## Comment 2

> Username: CamJN  
> Created_at: 2024-02-13 22:51:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/44#issuecomment-1942768835  

Just commenting in the hopes this gets looked at.

---
