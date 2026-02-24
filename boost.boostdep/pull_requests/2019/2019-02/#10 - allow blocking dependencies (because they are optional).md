# #10 allow blocking dependencies (because they are optional) [Merged]

> Username: HDembinski  
> Created at: 2019-02-13 20:26:53 UTC  
> Updated at: 2019-02-16 09:31:04 UTC  
> Merged at: 2019-02-15 19:52:34 UTC  
> Closed at: 2019-02-15 19:52:34 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10  

Adds a cmdline option to exclude dependencies found during scanning. I can use this to block known optional dependencies.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-02-13 23:34:06 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463419799  

I think that this should be `--ignore` instead of `--block`.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-02-14 08:50:54 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463544169  

Ok, but what do you use a shortcut then? `-I` is already taken.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2019-02-14 08:51:50 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463544445  

Feel free to edit this as you like, if you are generally ok with this feature.

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-02-14 13:11:03 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463621285  

`-N` should work.

---

## Comment 5

> Username: HDembinski  
> Created_at: 2019-02-14 20:01:13 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463771824  

Thinking about it more, I realized a flaw in this approach. The flag should only ignore primary dependencies. If the module enters through a secondary dependency, it should be kept.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-02-14 20:08:06 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463774390  

There are arguments both ways. You could very well wish to ignore a module that is an optional and unneeded dependency of one of your primary dependencies. So I'm inclined to think that this is correct as written. (If you want to keep a secondary dependency, you can just remove the `-N` option; but if the option only ignored primary dependencies, there would be no way to ignore a secondary dependency.)

---

## Comment 7

> Username: HDembinski  
> Created_at: 2019-02-14 20:23:14 UTC  
> Updated_at: 2019-02-15 08:06:29 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463780133  

My use case for this flag is basically to add a workaround, because depinst doesn't know about preprocessor flags. In my own library, I know that a dependency found by depinst is actually optional, but it will be difficult to figure that out for all the secondary libraries. And even so, the library may enter again at the third level or the forth... I think ignoring a library completely is very brittle. Ignoring it at the primary level is not and solves my problem.

---

## Comment 8

> Username: HDembinski  
> Created_at: 2019-02-14 20:24:08 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-463780496  

Besides, the implementation is much easier now.

---

## Comment 9

> Username: HDembinski  
> Created_at: 2019-02-16 09:31:03 UTC  
> Url: https://github.com/boostorg/boostdep/pull/10#issuecomment-464324130  

thanks!

---
