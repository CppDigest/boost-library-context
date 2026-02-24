# #188 Doc work [Closed]

> Username: sdkrystian  
> Created at: 2020-08-23 01:29:21 UTC  
> Updated at: 2020-08-23 22:23:46 UTC  
> Closed at: 2020-08-23 22:23:46 UTC  
> Url: https://github.com/boostorg/json/pull/188  



---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-08-23 01:30:44 UTC  
> Url: https://github.com/boostorg/json/pull/188#issuecomment-678717283  

@vinniefalco We can remove the "Using numbers" section, since I don't think we need to teach people how to use integers and doubles :)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-08-23 01:36:20 UTC  
> Url: https://github.com/boostorg/json/pull/188#issuecomment-678717625  

An automated preview of the documentation is available at [http://188.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://188.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-08-23 19:32:27 UTC  
> Updated_at: 2020-08-23 19:32:43 UTC  
> Url: https://github.com/boostorg/json/pull/188#issuecomment-678814912  

I don't think we can remove "Using Numbers." We have to fill it in, if for no other reason than to explain the range and distinction for how the container treats numbers as different variant discriminators. This is also the place where we need to explain `number_cast` and related functions.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-08-23 19:32:56 UTC  
> Url: https://github.com/boostorg/json/pull/188#issuecomment-678814961  

If you have changes please put them in a new PR since I am already working on merging this.

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-23 19:33:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/188#pullrequestreview-473036375  

📁 doc/qbk/02_10_init_lists.qbk

```diff
  14 | Initializer lists can be used to construct or
  15 |- assign objects of type __value__:
  15 |+ assign JSON __value__'s:
```

> Username: vinniefalco  
> Created_at: 2020-08-23 19:33:27 UTC  
> Updated_at: 2020-08-23 19:33:28 UTC  
> Url: https://github.com/boostorg/json/pull/188#discussion_r475256280  

no this doesn't work, `values's` should not be possessive.


---
