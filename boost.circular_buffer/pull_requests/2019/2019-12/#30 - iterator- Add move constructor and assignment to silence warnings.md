# #30 iterator: Add move constructor and assignment to silence warnings [Closed]

> Username: HazardyKnusperkeks  
> Created at: 2019-12-08 13:13:35 UTC  
> Updated at: 2021-12-11 14:00:11 UTC  
> Closed at: 2021-12-11 14:00:11 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/30  

This should fix GCCs -Wdeprecated-copy warnings, while maintaining C++03 compatibility.

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-12-11 14:00:11 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/30#issuecomment-991653852  

Should hopefully be addressed by 05a83223e4494edd86d099b672eba4367b45677b

---
