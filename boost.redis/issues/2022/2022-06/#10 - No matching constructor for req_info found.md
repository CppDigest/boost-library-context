# #10 - No matching constructor for req_info found [Closed]

> Username: Eddie-cz  
> Created at: 2022-06-27 10:04:48 UTC  
> Updated at: 2022-07-02 21:42:19 UTC  
> Closed at: 2022-07-02 21:41:56 UTC  
> Url: https://github.com/boostorg/redis/issues/10  

Add constructor for req_info to prevent compile errors under clang.  
Solution: Add req_info(const executor_type& ex) : timer(ex) {} and adjust constructor calls accordingly.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-06-27 19:30:08 UTC  
> Url: https://github.com/boostorg/redis/issues/10#issuecomment-1167791654  

Can you please share your compiler version? Thanks.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2022-06-27 20:16:56 UTC  
> Url: https://github.com/boostorg/redis/issues/10#issuecomment-1167839123  

It's clang-cl 14.0.6 in msvc env

---

## Comment 3

> Username: mzimbres  
> Created at: 2022-07-02 21:42:19 UTC  
> Url: https://github.com/boostorg/redis/issues/10#issuecomment-1172966103  

Fixed in the master.
