# #181 Binary find performance [Closed]

> Username: roshanrags  
> Created at: 2014-07-10 12:50:37 UTC  
> Updated at: 2014-08-09 17:19:56 UTC  
> Closed at: 2014-07-18 07:18:23 UTC  
> Url: https://github.com/boostorg/compute/pull/181  



---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-10 13:35:09 UTC  
> Url: https://github.com/boostorg/compute/pull/181#issuecomment-48604556  

[![Coverage Status](https://coveralls.io/builds/952359/badge)](https://coveralls.io/builds/952359)  
  
Coverage decreased (-0.32%) when pulling **cfbaa81314ad5b96e843ff2f9c2761d7fe5a8d72 on roshanr95:binary_find** into **bfe2bdd7f77644350d76f39a0be49feff79653a9 on kylelutz:master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-07-11 03:06:16 UTC  
> Updated_at: 2014-07-11 03:26:51 UTC  
> Url: https://github.com/boostorg/compute/pull/181#issuecomment-48689907  

Looks cool! Have you measured the performance increase? How much gain over simply calling `find_if()`?  
  
I'm going to hold off merging any performance changes until after the `0.3` release (should be early next week). Until then I am just going to be working on bug-fixes, testing, and documentation (but don't let that stop you from working in your own branches :-)).

---

## Comment 3

> Username: roshanrags  
> Created_at: 2014-07-12 13:34:21 UTC  
> Url: https://github.com/boostorg/compute/pull/181#issuecomment-48811596  

~7x gain for 2^25 elements(from ~20ms to ~3ms) . More importantly, the complexity changes from linear to logarithmic...

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-07-12 19:52:01 UTC  
> Url: https://github.com/boostorg/compute/pull/181#issuecomment-48822211  

Awesome! I'll get this merged in once `v0.3` is released.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-07-16 06:33:35 UTC  
> Url: https://github.com/boostorg/compute/pull/181#issuecomment-49128563  

Can you re-base this on `master` and then make a pull-request against `develop`?

---

## Comment 6

> Username: roshanrags  
> Created_at: 2014-07-18 07:17:56 UTC  
> Updated_at: 2014-07-18 07:18:20 UTC  
> Url: https://github.com/boostorg/compute/pull/181#issuecomment-49402237  

Done. Will open a new pull...

---

## Comment 7

> Username: coveralls  
> Created_at: 2014-07-18 07:18:32 UTC  
> Url: https://github.com/boostorg/compute/pull/181#issuecomment-49402270  

[![Coverage Status](https://coveralls.io/builds/980670/badge)](https://coveralls.io/builds/980670)  
  
Coverage decreased (-0.32%) when pulling **92ae416a32ce5f515c2a2ad06d09fa1ea0bc5f29 on roshanr95:binary_find** into **f50e9c011005ef27a63760fc6b6d7a3f092f0b5d on kylelutz:master**.

---
