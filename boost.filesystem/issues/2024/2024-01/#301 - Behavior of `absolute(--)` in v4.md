# #301 - Behavior of `absolute("")` in v4 [Closed]

> Username: Pesa  
> Created at: 2024-01-07 20:01:49 UTC  
> Updated at: 2024-01-09 04:20:05 UTC  
> Closed at: 2024-01-08 19:58:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301  

(Like #300, I'm not sure if this is a bug or I'm misreading the documentation)  
  
`absolute()` on an empty path is defined as being equal to `absolute(base) / path()`. Let's assume `base` is `/foo`. With Boost Filesystem v4 we get the following:  
  
* `absolute(path())` returns `/foo`  
* `absolute(base) / path()` returns `/foo/`  
  
This is because the behavior of `operator/=` changed in v4. Which one is correct?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-01-07 20:57:33 UTC  
> Updated at: 2024-01-07 20:57:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1880169078  

> Which one is correct?  
  
Both are, according to path modification rules in v3 and v4. This is intended behavior.

---

## Comment 2

> Username: Pesa  
> Created at: 2024-01-07 21:21:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1880177725  

Sorry, I'm not sure I understand. What do you mean that both are correct? Is the documentation of `absolute` wrong for v4 then?

---

## Comment 3

> Username: Lastique  
> Created at: 2024-01-07 21:39:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1880183822  

In what way is it wrong?

---

## Comment 4

> Username: Pesa  
> Created at: 2024-01-07 21:48:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1880186420  

It says:  
  
> Returns: A absolute path composed according to the following table [...] `return absolute(base) / p`  
  
(I'm looking at the bottom right cell)  
  
Not sure what "according to" means formally, but as I said earlier, for an empty path `p`, `absolute(p)` and `absolute(base) / p` give different results in v4.

---

## Comment 5

> Username: Lastique  
> Created at: 2024-01-07 21:55:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1880187780  

I'm not sure what the problem is. Yes, the result is different between v3 and v4, and that is as expected. The v4 result is correct - in v4.

---

## Comment 6

> Username: Pesa  
> Created at: 2024-01-08 00:18:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1880241701  

I'm not talking about v3, or the difference between v3 and v4. This entire issue is exclusively about v4.

---

## Comment 7

> Username: Lastique  
> Created at: 2024-01-08 12:54:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1880954150  

Ah, sorry, I misunderstood the original post. Yes, in v4, result should be `/foo/`.

---

## Comment 8

> Username: Pesa  
> Created at: 2024-01-09 04:20:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/301#issuecomment-1882389636  

Thanks!
