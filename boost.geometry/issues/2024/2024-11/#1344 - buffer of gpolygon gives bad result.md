# #1344 - buffer of gpolygon gives bad result [Closed]

> Username: gastald88  
> Created at: 2024-11-18 08:47:47 UTC  
> Updated at: 2024-11-25 18:47:44 UTC  
> Closed at: 2024-11-25 18:23:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1344  

I have a gpolygon and I apply a mitered buffer to inflate it.  
The result of the buffer vanishes.   
  
The gpolygon appears like the blue region in this image:  
![Image](https://github.com/user-attachments/assets/3589069e-9925-4793-94ff-e659610b8885)  
  
where in particular there are some tiny components within the magenta box.  
  
  
Here a godbolt link to reproduce the problem, with parameters passed to buffer call:  
https://godbolt.org/z/c3vjssbr4

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-11-25 18:23:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1344#issuecomment-2498739212  

Thanks for the report!  
  
But please remove the big miter limit: `join_strategy(1e4);` -> `join_strategy;`  
We don't check it (because we're not inclined to check these things), but it's way too large. The default is `5.0` and that one seems to work fine.  
  
Maybe the report, or behavior, can become better in the future, but let's first fix "real bugs".  
  
Closing.

---

## Comment 2

> Username: gastald88  
> Created at: 2024-11-25 18:47:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1344#issuecomment-2498785942  

Of course! sorry for the misuse and thanks for the response!
