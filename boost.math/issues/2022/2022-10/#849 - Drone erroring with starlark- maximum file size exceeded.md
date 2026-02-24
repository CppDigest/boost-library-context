# #849 - Drone erroring with starlark: maximum file size exceeded [Closed]

> Username: mborland  
> Created at: 2022-10-19 17:12:45 UTC  
> Updated at: 2022-10-21 03:53:42 UTC  
> Closed at: 2022-10-21 03:53:42 UTC  
> Url: https://github.com/boostorg/math/issues/849  

[This started two days ago](https://drone.cpp.al/boostorg/math) without any significant changes to the drone script (https://github.com/boostorg/math/commit/aa242e5639b61e95d10f53974a0d4285e800249e fixed a spelling error). @sdarwin do you see anything out of the ordinary?

---

## Comment 1

> Username: sdarwin  
> Created at: 2022-10-19 20:25:30 UTC  
> Url: https://github.com/boostorg/math/issues/849#issuecomment-1284533031  

Thanks for reporting the issue. There was a change, about two days ago on the drone server side, which would explain how the starlark file became larger, and then result in "starlark: maximum file size exceeded".  Reverting that, on the server.
