# #1271 [strategies] Fix spherical closest_points strategy for PointOfSegment different than model::point [Merged]

> Username: awulkiew  
> Created at: 2024-04-05 20:43:37 UTC  
> Updated at: 2024-07-24 13:45:34 UTC  
> Merged at: 2024-07-24 13:45:34 UTC  
> Closed at: 2024-07-24 13:45:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1271  

Currently it fails to compile due to multiple return types.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2024-04-08 13:30:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1271#pullrequestreview-1986464845  

thanks!

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2024-04-24 13:58:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1271#pullrequestreview-2019965554  

Thanks  
(we could add a test for this situation)

---

## Comment 3

> Username: awulkiew  
> Created_at: 2024-05-11 11:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1271#issuecomment-2105696609  

> Thanks (we could add a test for this situation)  
  
@barendgehrels would you prefer me to create a separate test case for user-defined types or maybe to replace BG models in all of them? If it's the latter then maybe we should use types different than BG models in all of the tests?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2024-05-11 13:16:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1271#issuecomment-2105742594  

> > Thanks (we could add a test for this situation)  
>   
> @barendgehrels would you prefer me to create a separate test case for user-defined types or maybe to replace BG models in all of them? If it's the latter then maybe we should use types different than BG models in all of the tests?  
  
It would indeed be good that every algorithm also contains one test for one different type. To test type diversity. The algorithms themselves can be tested with the standard type.  
  
So in this case I would suggest to create a separate test case for user-defined types.  
  
There is often also constness etc involved (I'm not asking that right now). Many of our test (not generating points) will not compile, probably. And algorithms working on two or three input/output's might contain a test case where different types (or even coordinate types) are involved. But most tests don't do this.  
  
I made this remark because there was a particular bug fixed related to this - then it would be good to add such a case (if you have the time).

---
