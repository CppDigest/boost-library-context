# #538 Added an example for displaying all standard morphological transformations [Closed]

> Username: meshtag  
> Created at: 2021-01-12 12:06:38 UTC  
> Updated at: 2021-01-21 19:45:54 UTC  
> Closed at: 2021-01-21 19:45:54 UTC  
> Url: https://github.com/boostorg/gil/pull/538  

### Description  
  
This pull request adds an example showing standard morphological transformations in the Boost Gil library.  
  
Summary of applied morphological concepts:  
1.Dilation:If 1 or more bright pixels coincide with the structuring element during convolution,  
center pixel is made bright.We can vary the number of times dilation happens by varying the   
argument 'iterations' in the dilate function.  
2.Erosion:If 8 or more bright pixels coincide with the structuring element during convolution,  
center pixel is made bright.We can vary the number of times erosion happens by varying the   
argument 'iterations' in the erode function.  
3.Opening:Opening is just another name of erosion followed by dilation.  
It is useful in removing noise.  
4.Closing:Closing is reverse of Opening, Dilation followed by Erosion.  
It is useful in closing small holes inside the foreground objects, or small black points   
on the object.  
5.Morphological Gradient:It is the difference between dilation and erosion of an image.  
The result will look like the outline of the object.  
6.Top Hat:It is the difference between input image and Opening of the image.  
7.Black Hat:It is the difference between the closing of the input image and input image.  
Functions have been made for applying above morphological transformations which  
return the transformed image.  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-12 18:25:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/538#pullrequestreview-566552167  

Before we dig into the code...  
  
> This pull request adds all standard morphological transformations to the Boost Gil library.  
  
The PR seems to be proposing an example and not an implementation of a feature for core or extension of the GIL library.  
  
I suggest you make your intention clearer.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-01-12 19:32:41 UTC  
> Updated_at: 2021-01-21 19:28:41 UTC  
> Url: https://github.com/boostorg/gil/pull/538#issuecomment-758886166  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/538?src=pr&el=h1) Report  
> Merging [#538](https://codecov.io/gh/boostorg/gil/pull/538?src=pr&el=desc) (48436fc) into [develop](https://codecov.io/gh/boostorg/gil/commit/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c?el=desc) (0c0fe1a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/gil/pull/538/graphs/tree.svg?width=650&height=150&src=pr&token=rbI5NTc0vX)](https://codecov.io/gh/boostorg/gil/pull/538?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #538   +/-   ##  
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
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/gil/pull/538?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/gil/pull/538?src=pr&el=footer). Last update [0c0fe1a...7e779a9](https://codecov.io/gh/boostorg/gil/pull/538?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: meshtag  
> Created_at: 2021-01-13 03:27:48 UTC  
> Url: https://github.com/boostorg/gil/pull/538#issuecomment-759180505  

> Before we dig into the code...  
>   
> > This pull request adds all standard morphological transformations to the Boost Gil library.  
>   
> The PR seems to be proposing an example and not an implementation of a feature for core or extension of the GIL library.  
>   
> I suggest you make your intention clearer.  
  
Point noted @mloskot

---

## Comment 4

> Username: meshtag  
> Created_at: 2021-01-13 14:31:03 UTC  
> Updated_at: 2021-01-13 14:32:40 UTC  
> Url: https://github.com/boostorg/gil/pull/538#issuecomment-759485921  

@mloskot ,my purpose of keeping all those functions in a single file is that i believe it would be somewhat easier for the reviewers to review my code when all of it is kept at a single place.I intend to integrate those functions with the library and add them as features once they are approved.

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-01-16 22:07:31 UTC  
> Updated_at: 2021-01-16 22:08:01 UTC  
> Url: https://github.com/boostorg/gil/pull/538#issuecomment-761687325  

@meshtag This is not a good approach I'm afraid.  
You want to submit PR that is what you actually intent to submit and get merged.  
Then, your PR is corrected and refined during review-change iterations until we all agree your PR is ready to merge.  
So, if you are still interested in submitting a PR then please follow our guidelines, ask questions if you need  
  
- https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#pull-requests  
- https://github.com/boostorg/gil/wiki/Guidelines  
  
and update this PR and re-request review.

---
