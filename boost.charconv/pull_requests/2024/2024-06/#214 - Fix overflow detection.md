# #214 Fix overflow detection [Merged]

> Username: mborland  
> Created at: 2024-06-28 18:25:45 UTC  
> Updated at: 2024-06-28 19:50:40 UTC  
> Merged at: 2024-06-28 19:50:37 UTC  
> Closed at: 2024-06-28 19:50:37 UTC  
> Url: https://github.com/boostorg/charconv/pull/214  

Closes: #212   
  
`nd` had an assumption about the number of digits in base-10. Now we use base2 digits * log_base(2) to correctly adjust the cutoff where we know overflow cannot occur

---
