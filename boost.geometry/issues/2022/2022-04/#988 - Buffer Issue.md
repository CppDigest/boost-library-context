# #988 - Buffer Issue [Closed]

> Username: danston  
> Created at: 2022-04-05 13:02:02 UTC  
> Updated at: 2022-08-03 08:24:56 UTC  
> Closed at: 2022-08-03 08:24:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/988  

When running `boost::geometry::buffer` on the given input set (see the Figure below), the result is not valid. The input seems to be quite reasonable without complex parts. Moreover, other similar inputs succeed. I have created a simple minimal example in [this gist](https://gist.github.com/danston/fbdcb04e9f92114995a62c31937dce82) to illustrate the issue. The last two points in the input are duplicates but even when removing them and having an open polyline, the code still fails to produce the valid result. Could you please clarify if this is a bug or I do something wrong here? Thank you in advance!  
  
Tested with: Boost 1.77 and Boost 1.78.  
  
![input](https://user-images.githubusercontent.com/8123586/161758623-21cd4b41-a25c-43c2-8de1-3926285915b9.png)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-04-08 14:53:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/988#issuecomment-1092953388  

Thanks, I can reproduce it and it is indeed wrong (also in 1.79).  
Will work on it.  
  
![image](https://user-images.githubusercontent.com/334849/162464694-7a8ee6b0-60d6-41bb-916d-939ad69342ce.png)

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-04-08 15:00:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/988#issuecomment-1092960676  

> Could you please clarify if this is a bug or I do something wrong here?   
  
Your code is right. It's a bug. It internally classifies to many intersection points as acceptable and therefore creates an additional (wrong) path.

---

## Comment 3

> Username: danston  
> Created at: 2022-04-08 15:38:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/988#issuecomment-1093016274  

@barendgehrels Thanks a lot for looking into this issue!
