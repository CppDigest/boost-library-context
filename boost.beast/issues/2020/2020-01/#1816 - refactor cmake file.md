# #1816 - refactor cmake file [Closed]

> Username: vinniefalco  
> Created at: 2020-01-21 16:52:35 UTC  
> Updated at: 2024-11-25 08:11:42 UTC  
> Closed at: 2024-11-25 08:11:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1816  

The Beast CMakeLists.txt needs to be updated using one in JSON as a template:  
https://github.com/vinniefalco/json/blob/develop/CMakeLists.txt  
  
Don't forget to update the README.md with the necessary lines:  
https://github.com/vinniefalco/json/#cmake

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-27 09:05:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-578653159  

There are some additional operations in the boost beast CMakeLists.txt file.  
I'd value a discussion with you prior to proceeding.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-01-27 13:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-578739219  

Can you just briefly enumerate what those operations are?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-02-27 05:33:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-591790492  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: ricejasonf  
> Created at: 2020-06-18 00:46:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-645702204  

Building tests and examples by default is cumbersome, and everyone has their own one-off way of disabling them that have to be specified.  
  
Also, I noticed you can not build using cmake without OpenSSL:  
```  
CMake Error: The following variables are used in this project, but they are set to NOTFOUND.  
Please set them or make sure they are set and tested correctly in the CMake files:  
OPENSSL_CRYPTO_LIBRARY (ADVANCED)  
    linked by target "lib-beast" in directory /usr/local/src/beast-6f57e5934c1f307e4510ee11dd09594526a3f5d0  
  
... (more libraries follow)  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-06-18 00:57:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-645704877  

Use bjam

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-06-18 00:57:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-645705009  

I agree this needs to be addressed

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:42:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-1256871950  

Same as #2472.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-09-25 00:32:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-1257090622  

@grisumbras needs to do it, or someone needs to make it like the JSON and URL CMLs

---

## Comment 9

> Username: grisumbras  
> Created at: 2022-09-25 08:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-1257145035  

Ok, I'll get to it on Monday.

---

## Comment 10

> Username: grisumbras  
> Created at: 2022-09-26 09:01:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-1257718642  

What usage variants does Beast support?  
1. Standalone build (outside of Boost tree, as a top-level project).  
2. Boost build (inside Boost tree, under superproject).  
4. Integrated standalone build (as a subdirectory of a user build, not under superpoject, and not as a Boost lib).  
5. Integrated boost build (as a subdirectory of a user build, not under superpoject, but as a Boost lib).  
  
Is that all?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-09-26 18:16:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-1258428261  

I'm not sure. I want as few variants as possible. I can only think of 2 that are required. One is the normal in-superproject which is used to build the releases. The other is the author/maintainer workflow. This might even only be one variant. I would like it to be like JSON, URL, and Http-Proto where the lib is its own target, the dependencies are cleanly specified, and so on

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2022-09-26 18:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-1258454723  

I don't think anything but (2) make sense tbh, since beast doesn't have a standalone version.

---

## Comment 13

> Username: grisumbras  
> Created at: 2022-09-27 05:19:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1816#issuecomment-1258991211  

Oh, I was under impression Beast has a standalone version.
