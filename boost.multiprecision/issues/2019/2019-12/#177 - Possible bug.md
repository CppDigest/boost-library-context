# #177 - Possible bug [Closed]

> Username: Milerius  
> Created at: 2019-12-26 22:23:15 UTC  
> Updated at: 2021-11-02 18:01:46 UTC  
> Closed at: 2021-11-02 18:01:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177  

https://github.com/boostorg/multiprecision/blob/632f68400c28e85d62171c40b16d71699ac6dfd7/include/boost/multiprecision/number.hpp#L2137  
  
according to https://www.viva64.com/en/w/v739/ this is dangerous.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2019-12-27 09:50:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177#issuecomment-569234841  

John, what's your opinion on this?  
I agree with the report. I could try for a fix for 1.73 if you like?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2019-12-27 09:58:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177#issuecomment-569236197  

I think meant 1.74 I believe, later in the future.

---

## Comment 3

> Username: madhur4127  
> Created at: 2019-12-27 14:37:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177#issuecomment-569282266  

I think fix suggested in article itself is quite straightforward - change type of `c` to `int`. Also `peek()` returns `int` so `static_cast` should be risky.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-12-27 14:45:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177#issuecomment-569283702  

+1 on changing char to int, though to be honest the chances of hitting the issue when parsing *numbers* is probably vanishingly small?

---

## Comment 5

> Username: Milerius  
> Created at: 2019-12-27 15:08:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177#issuecomment-569287819  

It is true that the problem is unlikely to occur, however it is better to prevent. Now that we know that there is a potential bug even if it is unlikely to happen better to solve it.

---

## Comment 6

> Username: ghost  
> Created at: 2021-11-01 22:48:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177#issuecomment-956797206  

@ckormanyos Fixed in https://github.com/boostorg/multiprecision/commit/9e7fc3077c663c0cfc19fa40aab41cacf62417d7

---

## Comment 7

> Username: ckormanyos  
> Created at: 2021-11-02 18:01:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/177#issuecomment-957996361  

Thanks @borisssoft. Closed.
