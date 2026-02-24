# #1244 - Difference returns wrong result if hole touch the boundary of subtrahend [Closed]

> Username: evgeniyfeder  
> Created at: 2024-02-22 18:36:39 UTC  
> Updated at: 2024-04-01 10:22:27 UTC  
> Closed at: 2024-04-01 10:22:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1244  

Hello  
Actual result: Purple - orange = green.  
<img src="https://github.com/boostorg/geometry/assets/7823804/a08ad779-e0d1-47f6-b0b7-f68e9af0622c" width="300" height="300">  
<img src="https://github.com/boostorg/geometry/assets/7823804/31e6fc4f-e0af-48c6-a48f-6d3c1602916f" width="300" height="300">  
Expected result: blue  
<img src="https://github.com/boostorg/geometry/assets/7823804/6e233a1f-26f3-41e5-bbe0-687ac948b3a9" width="300" height="300">  
  
I noticed that the problem was added in 1.77->1.78. Related PR could be: https://github.com/boostorg/geometry/pull/887  
  
https://godbolt.org/z/s7zKKbG61

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-03-03 09:26:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1244#issuecomment-1975099459  

Thanks for the report and the potential related PR, I can reproduce it. Clockwise is fine, but counter clockwise is wrong.

---

## Comment 2

> Username: vissarion  
> Created at: 2024-04-01 10:22:13 UTC  
> Updated at: 2024-04-01 10:22:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/1244#issuecomment-2029538737  

Fixed by https://github.com/boostorg/geometry/pull/1263
