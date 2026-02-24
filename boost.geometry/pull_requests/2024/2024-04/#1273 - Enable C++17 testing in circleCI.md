# #1273 Enable C++17 testing in circleCI [Closed]

> Username: vissarion  
> Created at: 2024-04-11 11:25:59 UTC  
> Updated at: 2024-08-14 21:06:09 UTC  
> Closed at: 2024-08-14 21:06:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1273  

This PR enables testing in circleCI the C++17 dependent code, following the discussion here: https://github.com/boostorg/geometry/pull/1265#discussion_r1544804957

---

## Comment 1

> Username: awulkiew  
> Created_at: 2024-04-19 21:26:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1273#issuecomment-2067302530  

Would it be possible to force b2 to only compile the sources instead of running the tests?  
I'm thinking about some mechanism automatically replacing `run` and `link` directive(?) with `compile` for targets in Jamfiles.

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2024-04-24 13:57:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1273#pullrequestreview-2019962217  

Thanks

---

## Comment 3

> Username: vissarion  
> Created_at: 2024-05-09 16:05:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1273#issuecomment-2102961622  

I have made a try, actually editing Jamfiles. I couldn't find a simpler way; it seems that b2 does not have an only build command.   
  
An alternative could be to use cmake.

---

## Comment 4

> Username: vissarion  
> Created_at: 2024-08-14 21:06:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1273#issuecomment-2289900526  

cmake provides a much simpler way to do this (see https://github.com/boostorg/geometry/pull/1302) so I am closing this PR

---
