# #16 - Integer <-> Optional cycle [Closed]

> Username: glenfe  
> Created at: 2018-12-04 01:39:24 UTC  
> Updated at: 2018-12-05 10:07:54 UTC  
> Closed at: 2018-12-05 10:07:53 UTC  
> Url: https://github.com/boostorg/integer/issues/16  

As of the following commit:  
https://github.com/boostorg/integer/commit/fc4d6572019e3d61dd496b9f7a5423a4b1419eeb#r31545077  
Integer depends on Optional depends on [Utility depends on Container_hash depends on] Integer.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-12-04 09:49:09 UTC  
> Updated at: 2018-12-04 09:49:29 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444037862  

Any suggestions on how to fix this? I wouldn't want to revert the commit. I'm leaning towards further decoupling Boost.Utility, though I'm not sure how effective that would be.

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-12-04 16:15:14 UTC  
> Updated at: 2018-12-04 16:16:33 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444158433  

A very simple fix would be just to return a zero to indicate failure, rather than an optional. Another option is to use std::optional.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-12-04 16:27:14 UTC  
> Updated at: 2018-12-04 16:27:39 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444162994  

If zero can never be a valid result of the algorithm, sure. `std::optional` I would like to avoid.

---

## Comment 4

> Username: NAThompson  
> Created at: 2018-12-04 16:41:42 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444168295  

Zero can indeed never be a valid result of the algorithm.

---

## Comment 5

> Username: Lastique  
> Created at: 2018-12-04 16:58:10 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444174443  

Ok, would you prepare a PR, please?

---

## Comment 6

> Username: NAThompson  
> Created at: 2018-12-04 17:27:03 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444184747  

Yes, I will fix this.

---

## Comment 7

> Username: glenfe  
> Created at: 2018-12-04 17:28:46 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444185386  

> If zero can never be a valid result of the algorithm, sure. std::optional I would like to avoid.  
  
That sounds better than what I was going to suggest (a custom optional like type in Integer for use as the result of this function).  
  
Thanks Nick and Andrey.

---

## Comment 8

> Username: NAThompson  
> Created at: 2018-12-04 17:58:16 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444195856  

I generally like to force people to do error handling using optional, but even I felt like this was a bit excessive for this particular case. That fact that it descends us into dependency hell made the case very clear.

---

## Comment 9

> Username: NAThompson  
> Created at: 2018-12-04 18:04:07 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444197848  

Note that if the `boost::optional<Z>` gets into 1.69, then we've just made a backward incompatible change. Will the release manager allow us to put this into 1.69?

---

## Comment 10

> Username: glenfe  
> Created at: 2018-12-04 18:16:27 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444201899  

The change that added the dependency on Optional is not in master so shouldn't be releasing with 1.69. So no compatibility will be broken.

---

## Comment 11

> Username: Lastique  
> Created at: 2018-12-05 10:07:53 UTC  
> Url: https://github.com/boostorg/integer/issues/16#issuecomment-444430446  

Resolved by https://github.com/boostorg/integer/pull/17.
