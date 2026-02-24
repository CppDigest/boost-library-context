# #539 Added functionality for drawing custom rectangles and circles  [Closed]

> Username: meshtag  
> Created at: 2021-01-12 16:28:14 UTC  
> Updated at: 2021-02-02 08:49:05 UTC  
> Closed at: 2021-02-02 08:49:04 UTC  
> Url: https://github.com/boostorg/gil/pull/539  

### Description  
  
This pull request intends to add an example of drawing customizable rectangles and circles in Boost Gil.  
The properties which are necessary to be specified are:  
1)Rectangle:input_image_view,upper left corner co-ordinates,bottom right corner co-ordinates,colour of border,thickness of border.  
2)Circle:input_image_view,co-ordinates of center,radius,colour of border,thickness of border.  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-12 18:25:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/539#pullrequestreview-566552436  

> This pull request intends to add functionality of  
  
The PR seems to be proposing an example, not an implementation of a feature for core or extension of the GIL library.  
  
I suggest you make your intention clearer.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-01-13 01:18:27 UTC  
> Updated_at: 2021-01-30 05:15:50 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-759139746  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/539?src=pr&el=h1) Report  
> Merging [#539](https://codecov.io/gh/boostorg/gil/pull/539?src=pr&el=desc) (2af8511) into [develop](https://codecov.io/gh/boostorg/gil/commit/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c?el=desc) (0c0fe1a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/gil/pull/539/graphs/tree.svg?width=650&height=150&src=pr&token=rbI5NTc0vX)](https://codecov.io/gh/boostorg/gil/pull/539?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #539   +/-   ##  
========================================  
  Coverage    77.81%   77.81%             
========================================  
  Files          110      110             
  Lines         4367     4367             
========================================  
  Hits          3398     3398             
  Misses         969      969             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/gil/pull/539?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/gil/pull/539?src=pr&el=footer). Last update [422ca82...4ed7e48](https://codecov.io/gh/boostorg/gil/pull/539?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: meshtag  
> Created_at: 2021-01-13 03:26:02 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-759179975  

> > This pull request intends to add functionality of  
>   
> The PR seems to be proposing an example, not an implementation of a feature for core or extension of the GIL library.  
>   
> I suggest you make your intention clearer.  
  
Point noted @mloskot

---

## Comment 4

> Username: meshtag  
> Created_at: 2021-01-13 14:33:55 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-759487806  

@mloskot ,my purpose of keeping all those functions in a single file is that i believe it would be somewhat easier for the reviewers to review my code when all of it is kept at a single place.I intend to integrate those functions with the library and add them as features once they are approved.

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-01-16 22:07:51 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-761687378  

@meshtag See https://github.com/boostorg/gil/pull/538#issuecomment-761687325

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-01-28 21:55:44 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-769425117  

@meshtag If you sync this PR with `develop`, then you'll get the CI builds update (removed Travis CI, added GitHub Actions).

---

## Comment 7

> Username: meshtag  
> Created_at: 2021-01-30 05:28:24 UTC  
> Updated_at: 2021-01-30 05:30:00 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-770160793  

@mloskot,I still have work to do on this PR, I will try to improve it based on my experience with PR 541 (morphological operations one) . I therefore request you to review it later once I am done with modifications. I will ping you/start a thread on slack when my work is ready for review .

---

## Comment 8

> Username: mloskot  
> Created_at: 2021-01-30 14:47:22 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-770222801  

@meshtag No problem, take your time and request review when you're ready

---

## Comment 9

> Username: lpranam  
> Created_at: 2021-01-31 05:31:07 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-770330044  

@meshtag in that case you can use draft request to show work in progress and then convert it into regular PR once done. nice GH feature to use.

---

## Comment 10

> Username: simmplecoder  
> Created_at: 2021-02-01 08:32:15 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-770673634  

Since #512 has been merged, there is functionality for rasterizing lines and circles. It might have a bit quirky interface, but that should work reasonably well. Please use those if they are not too problematic for you. [Rasterization functionality](https://github.com/boostorg/gil/tree/develop/include/boost/gil/rasterization).

---

## Comment 11

> Username: meshtag  
> Created_at: 2021-02-02 08:49:04 UTC  
> Url: https://github.com/boostorg/gil/pull/539#issuecomment-771472729  

Okay since better work is already done on this topic , I am closing this pull request .

---
