# #671 Add point_xyz as pre-defined geometry model [Merged]

> Username: digu-007  
> Created at: 2020-02-29 16:25:09 UTC  
> Updated at: 2020-03-05 07:44:35 UTC  
> Merged at: 2020-03-04 22:18:17 UTC  
> Closed at: 2020-03-04 22:18:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/671  

This PR fixes #655. I have added the `model::d3::point_xyz` with example.  
  
I have verified the documentation, here is the result:  
![point_xyz_1](https://user-images.githubusercontent.com/31317843/75611309-760f4180-5b3f-11ea-9bb5-c6f04d26e33c.png)  
![point_xyz_2](https://user-images.githubusercontent.com/31317843/75611312-7a3b5f00-5b3f-11ea-83b4-eddd8351e95b.png)

---

## Comment 1

> Username: digu-007  
> Created_at: 2020-02-29 18:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-592974660  

@mloskot Can you review the PR?

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-02-29 23:30:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/671#pullrequestreview-366825344  

It would be good to accompany new type with unit tests, even something as basic as `test/geometries/box.cpp`.  
  
@awulkiew I realised there is no such test for `point_xy`,  and other basic models. Have they moved around or I'm not recalling it correctly there were some.

📁 doc/reference/geometries/point_xyz.qbk

```diff
  11 |+ [heading Examples]
  12 |+ [point_xyz]
  13 |+ [point_xyz_output]
```

> Username: mloskot  
> Created_at: 2020-02-29 23:22:12 UTC  
> Updated_at: 2020-03-01 11:19:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#discussion_r386062811  

Newline is missing


---

## Comment 3

> Username: digu-007  
> Created_at: 2020-03-01 11:23:17 UTC  
> Updated_at: 2020-03-01 11:24:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-593085813  

@mloskot I have added simple unit test for `point_xyz`.  
> I realised there is no such test for `point_xy`  
  
I couldn't find that either. If the changes gets accepted here, then I can add similar tests for `point_xy` also.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-03-01 15:33:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-593110240  

@mloskot I don't recall moving the tests. I think there were no explicit tests for the model. However they are used in all other tests, including `point_xy`.

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-03-01 17:52:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-593124825  

@awulkiew Right. I just had an impression there used to be basic unit tests for models, before they actually got covered by tests of algorithms, etc.  
  
I, personally, see value in basic unit tests. They clearly show what operations on model classes are actually tested. Not something that can be easily displayed from all the complex tests, unless one knows what to grep through the code for. If there is no objection, e.g. it would hit CI performance, I think adding tests for models is a good thing, no?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2020-03-01 18:55:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-593131362  

@mloskot > I think adding tests for models is a good thing, no?  
  
Of course, I was just explaining the current state.

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-03-01 19:05:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-593132746  

@awulkiew Sure, thanks! I have opened related issue in #673

---

## Review 8 [Approved]

> Username: mloskot  
> Created_at: 2020-03-02 23:03:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/671#pullrequestreview-367594222  

I think this is ready to merge.  
  
I have built and tested all tests and examples locally using GCC 9, they compile without warnings.  
I have also built the docs and no issues have been reported for this content.  
  
We need second or more reviews to finally accept it.

---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2020-03-03 13:42:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/671#pullrequestreview-367986076  

Thanks! I am ok with merging.  
  
I build locally the docs and tests run successfully with gcc7

---

## Comment 10

> Username: mloskot  
> Created_at: 2020-03-04 22:18:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-594890337  

@digu-007 Thanks for your contribution.

---

## Comment 11

> Username: digu-007  
> Created_at: 2020-03-05 07:44:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/671#issuecomment-595075260  

@mloskot @awulkiew @vissarion Thanks for merging and guidance :)

---
