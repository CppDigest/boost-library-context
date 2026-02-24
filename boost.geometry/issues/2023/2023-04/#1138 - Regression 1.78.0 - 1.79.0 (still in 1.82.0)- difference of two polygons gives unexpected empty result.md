# #1138 - Regression 1.78.0 - 1.79.0 (still in 1.82.0): difference of two polygons gives unexpected empty result [Closed]

> Username: gastald88  
> Created at: 2023-04-21 10:07:42 UTC  
> Updated at: 2023-09-14 17:28:47 UTC  
> Closed at: 2023-09-14 08:36:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1138  

I have two polygons p1 and p2, where the difference p1 - p2 is clearly non-empty (see image, p1 blue, p2 orange), but the difference operation returns an empty polygon.  
  
![image](https://user-images.githubusercontent.com/37936526/233601244-b9849164-f69c-4fe0-b408-35766b321960.png)  
  
Here below a godbolt link to replicate this problem:  
https://godbolt.org/z/YTjM3GG1G  
  
The result is correct in boost version 1.78.0 (you can set at the godbolt platform in the compiler arguments `-I/opt/compiler-explorer/libs/boost_1_78_0)`  
  
From version 1.79.0 on (until the 1.82.0 that I recentely tested on my local environment) the result is incorrect.  
  
I also tried to define the macro BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE, but the bug is still there.

---

## Comment 1

> Username: vissarion  
> Created at: 2023-06-16 14:39:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1138#issuecomment-1594805852  

Thanks for filing this issue.  
  
I can reproduce your issue locally.    
  
However, defining BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE (before all includes) is working for me.  
This seems to be one regression of rescaling removal in 1.79.

---

## Comment 2

> Username: gastald88  
> Created at: 2023-06-16 15:53:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1138#issuecomment-1594903485  

Thanks for your answer,  
I have wrongly put the macro definitions after all includes rather than before. Once fixed this, it worked as you said.  
Thanks again!

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-09-14 08:36:50 UTC  
> Updated at: 2023-09-14 08:46:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1138#issuecomment-1719010669  

Fixed by commit eb879fe62 in PR #1193  
It now also works without rescaling.  
I will create a unit test and close the issue.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-09-14 17:28:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1138#issuecomment-1719863883  

Unit test in #1202
