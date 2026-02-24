# #210 value_builder optimization [Closed]

> Username: vinniefalco  
> Created at: 2020-08-26 12:26:52 UTC  
> Updated at: 2020-08-27 21:55:16 UTC  
> Closed at: 2020-08-27 02:41:25 UTC  
> Url: https://github.com/boostorg/json/pull/210  

Has a small leak unfortunately

---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-08-26 13:06:40 UTC  
> Url: https://github.com/boostorg/json/pull/210#issuecomment-680867061  

@vinniefalco Is the leak inherent to the approach? Or is it something we can fix cleanly.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-08-26 13:13:00 UTC  
> Url: https://github.com/boostorg/json/pull/210#issuecomment-680870656  

nah just a Plain ole' bug. I fixed the leak in the "tidy" commit. However, now we have this "maybe used uninitialized" which has got me stumped, have a look?

---

## Comment 3

> Username: sdkrystian  
> Created_at: 2020-08-26 13:14:41 UTC  
> Url: https://github.com/boostorg/json/pull/210#issuecomment-680871688  

Affirmative.

---
