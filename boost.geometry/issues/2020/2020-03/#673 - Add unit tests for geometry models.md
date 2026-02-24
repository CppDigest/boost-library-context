# #673 - Add unit tests for geometry models [Closed]

> Username: mloskot  
> Created at: 2020-03-01 19:05:23 UTC  
> Updated at: 2020-03-10 08:15:08 UTC  
> Closed at: 2020-03-10 08:15:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/673  

This task is about adding new tests for basic classes like geometry models (e.g. `point`, `point_xy`, etc.) to exercise their basic operations.  
  
Although we can be fairly sure most, if not all, are covered by the extensive tests of algorithms, there is a value of having separate unit tests for each of the models. Such tests clearly show (document!) what operations are actually tested. Not something that can be easily displayed from all the complex tests, unless one knows what to `grep` through the code for.  
  
A good place to start is to look at and follow the unit test for `box` in [test/geometries/box.cpp](https://github.com/boostorg/geometry/blob/develop/test/geometries/box.cpp).  
  
### References  
  
Covering basic classes like geometry models with unit tests have been discussed here  
https://github.com/boostorg/geometry/pull/671#issuecomment-593110240

---

## Comment 1

> Username: sudo-panda  
> Created at: 2020-03-02 20:14:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/673#issuecomment-593595804  

Is someone working on this or can I do it?

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-02 22:05:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/673#issuecomment-593647455  

@sudo-panda I'm not aware of anyone working on it. Only @digu-007  mentioned  https://github.com/boostorg/geometry/pull/671#issuecomment-593085813 adding test for `point_xy`.

---

## Comment 3

> Username: digu-007  
> Created at: 2020-03-02 22:55:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/673#issuecomment-593665834  

> @sudo-panda I'm not aware of anyone working on it. Only @digu-007 mentioned [#671 (comment)](https://github.com/boostorg/geometry/pull/671#issuecomment-593085813) adding test for `point_xy`.  
  
I was waiting for review in #671 and then I can send the PR for this issue as well.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-03-02 22:56:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/673#issuecomment-593666532  

@digu-007  A unit-test for `point_xy` is quite an independent task from PR #671

---

## Comment 5

> Username: digu-007  
> Created at: 2020-03-02 23:00:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/673#issuecomment-593667704  

> @digu-007 A unit-test for `point_xy` is quite an independent task from PR #671  
  
@mloskot Yes, but my re-review is about unit test of `point_xyz`. I'm waiting for approval of that part.

---

## Comment 6

> Username: mloskot  
> Created at: 2020-03-02 23:05:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/673#issuecomment-593669359  

@digu-007 That is clear. What I mean, that if you want to add unit test for `point_xy`, you can add it, as new PR, and you can add it separately from review and merge timeline of your PR.

---

## Comment 7

> Username: digu-007  
> Created at: 2020-03-02 23:15:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/673#issuecomment-593672468  

@mloskot Ok I'll send a PR tomorrow only, starting with `point_xy` and then I can work on the remaining models as well.
