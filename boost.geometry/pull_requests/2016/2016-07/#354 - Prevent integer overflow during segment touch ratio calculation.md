# #354 Prevent integer overflow during segment touch ratio calculation [Closed]

> Username: mcquay239  
> Created at: 2016-07-26 18:16:35 UTC  
> Updated at: 2024-10-30 20:39:09 UTC  
> Closed at: 2024-10-30 20:39:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/354  

I use boost/geometry clipping operations after snap rounding (to prevent robustness issues). So I have no intersecting segments in my areals, segments are intersecting with their endpoints. Integers are large (they can be about 2**30), but I use 64-bit integer coordinate type. So sides (side_info) are calculating perfectly, but when segment ratio calculation leads to integer overflow.  
  
But we don't actually need to calculate these ratios, they are trivial.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-07-26 20:11:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-235389671  

Thanks @mcquay239 !   
The compilation fixes can certainly be applied and will help some regressions in the regression matrix.  
The other fix can most probably be applied too - (have to spend more time on it to confirm, which I now don't have) but to be sure, did you run the unit tests?

---

## Comment 2

> Username: mcquay239  
> Created_at: 2016-07-26 21:05:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-235405286  

I did and I cought a regression in multiline/multiline intersection.  
Unfortunately I noticed it after the pull request, because there already  
were several test failures. I beleive it's something easy-to-fix and I'll  
try to debug it.  
  
вт, 26 июля 2016 г., 23:11 Barend Gehrels notifications@github.com:  
  
> Thanks @mcquay239 https://github.com/mcquay239 !  
> The compilation fixes can certainly be applied and will help some  
> regressions in the regression matrix.  
> The other fix can most probably be applied too - (have to spend more time  
> on it to confirm, which I now don't have) but to be sure, did you run the  
> unit tests?  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/geometry/pull/354#issuecomment-235389671,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AAxnDP8t82irmxL2CiM1vPxRheDiy4eIks5qZmnpgaJpZM4JVb1u  
> .

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2016-07-27 15:22:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-235620490  

OK - thanks again. If necessary we can also split this PR

---

## Comment 4

> Username: mcquay239  
> Created_at: 2016-07-28 15:34:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-235932535  

I've just fixed tests. The only notice is that a linestring, consisting of an isolated point (two equal points, actually), is not simple (really?). But as I understand it's an expected behaviour.  
  
Anyway all tests except broken earlier are passed.

---

## Comment 5

> Username: mcquay239  
> Created_at: 2016-07-28 15:40:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-235934424  

I don't know if it's necessary to split this PR: #353 is equivalent to https://github.com/boostorg/geometry/pull/354/commits/b5c07c67ba53c3916d59ade6b005806acde75325, you can apply #353 and then think about this one.

---

## Comment 6

> Username: mcquay239  
> Created_at: 2017-06-02 11:46:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-305764132  

Updated PR, checked tests once more, please apply   
@barendgehrels

---

## Comment 7

> Username: awulkiew  
> Created_at: 2021-06-03 15:06:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-853942336  

@barendgehrels Considering you've made many changes in the strategy since 2016 is this PR still relevant?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2024-09-30 18:35:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/354#issuecomment-2383902019  

> @barendgehrels Considering you've made many changes in the strategy since 2016 is this PR still relevant?  
  
Indeed, I probably missed this. No, I don't think it's still relevant. Let's close it - or maybe still compare it

---
