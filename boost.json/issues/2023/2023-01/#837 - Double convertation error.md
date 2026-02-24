# #837 - Double convertation error [Closed]

> Username: man-k28  
> Created at: 2023-01-11 10:40:49 UTC  
> Updated at: 2024-10-09 14:16:18 UTC  
> Closed at: 2024-10-09 14:16:18 UTC  
> Url: https://github.com/boostorg/json/issues/837  

When i save JSON object to ofstream in file, boost broken double value, as:  
"timestamp": 1673382441.141209  
->  
"timestamp": 1.673382441141209E9  
  
Tested on boost 1.81.0

---

## Comment 1

> Username: mclow  
> Created at: 2023-01-11 15:02:23 UTC  
> Url: https://github.com/boostorg/json/issues/837#issuecomment-1378893502  

That looks like the same value to me. What is the problem?

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-01-11 15:06:30 UTC  
> Url: https://github.com/boostorg/json/issues/837#issuecomment-1378899768  

Yes, Boost.JSON changed the value representation. But the value itself stayed the same.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-10-09 14:16:18 UTC  
> Url: https://github.com/boostorg/json/issues/837#issuecomment-2402475575  

Closing this for now. In the future we might add the ability to output numbers in fixed format.
