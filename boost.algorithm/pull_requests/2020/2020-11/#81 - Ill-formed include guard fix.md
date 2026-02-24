# #81 Ill-formed include guard fix [Closed]

> Username: tony-space  
> Created at: 2020-11-12 15:55:24 UTC  
> Updated at: 2020-11-12 16:33:35 UTC  
> Closed at: 2020-11-12 16:33:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/81  

I've discovered that if one includes transform_reduce.hpp and inclusive_scan.hpp they cannot use functions from the last included header.  
  
The reason is ill-formed include guard in inclusive_scan.hpp, originally copy-pasted from transform_reduce.hpp. I've fixed it.

---

## Comment 1

> Username: mclow  
> Created_at: 2020-11-12 16:07:11 UTC  
> Url: https://github.com/boostorg/algorithm/pull/81#issuecomment-726173837  

Thanks for the bug fix.  
I'm less enamored with the doc fix; I'm not sure that's an improvement.

---

## Comment 2

> Username: tony-space  
> Created_at: 2020-11-12 16:15:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/81#issuecomment-726179024  

> I'm less enamored with the doc fix; I'm not sure that's an improvement.  
  
Feel free to change the description as you want. I've copied the description from here:  
https://en.cppreference.com/w/cpp/algorithm/inclusive_scan  
  
A similar version is available here:  
https://thrust.github.io/doc/group__prefixsums_ga7109170b96a48fab736e52b75f423464.html

---

## Comment 3

> Username: mclow  
> Created_at: 2020-11-12 16:33:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/81#issuecomment-726189667  

Fixed (w/o the doc update) in 8c26a50. Thank you!

---
