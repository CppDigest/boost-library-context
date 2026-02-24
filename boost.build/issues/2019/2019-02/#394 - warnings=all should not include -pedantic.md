# #394 - warnings=all should not include -pedantic? [Closed]

> Username: pdimov  
> Created at: 2019-02-19 01:03:50 UTC  
> Updated at: 2019-03-02 23:44:25 UTC  
> Closed at: 2019-02-23 16:55:16 UTC  
> Url: https://github.com/boostorg/build/issues/394  

Currently `warnings=all` is `-Wall -pedantic` on GCC and Clang. At least on the latter, `-pedantic` lives up to its name and is uselessly pedantic. There is a useful non-pedantic level above `-Wall`, but it's enabled by `-Wextra`.  
  
In addition, the warning levels don't match between MSVC and GCC. On MSVC, `warnings=off` is `/W0`, `warnings=on` is `/W3` (the default) and `warnings=all` is `/W4` (which roughly corresponds to GCC `-Wall`). But on GCC, `warnings=on` is `-Wall`, which is rather more like `/W4` (and would better correspond to the feature value `all`.)  
  
Perhaps we could introduce two more feature values, `extra` and `pedantic`, and then use  
  
`off` - `-w`  
`on` - nothing (compiler default)  
`all` - `-Wall`  
`extra` - `-Wall -Wextra`  
`pedantic` - `-Wall -Wextra -pedantic`  
  
Aligning the values between compilers and adding a way to get at `-Wextra` would aid Jamfile portability.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-02-23 15:56:20 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-466664105  

The feature change for this is done: https://github.com/boostorg/build/commit/033ef59305c28c9a2b6d3e1c6820ce6b9a931aaf

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-02 22:38:20 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468967288  

This is what I was wanting to propose too. Thanks!

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-02 22:43:07 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468967602  

However in my thoughts `-pedantic` should be a subfeature, so you write `all-pedintic` or `extra-pedantic`.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-03-02 22:47:04 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468967842  

I however wonder about two things: one, whether warnings=on should still be -Wall for gcc, or the default; and two, whether we could somehow make it warnings=pedantic fall back to =extra, and =extra fall back to =all, to avoid fixing all toolsets to recognize the new values.  
  
I remember that there was a syntax `extra : all` when defining a feature that did that, but I may be misremembering.  
  
`pedantic` being a subfeature did occur to me too. Perhaps it's indeed better to have it this way.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-03-02 22:49:50 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468968024  

> `pedantic` being a subfeature did occur to me too. Perhaps it's indeed better to have it this way.  
  
After I wrote my comment I reread `extra-pedantic` and it looks so funny and ambiguous, so I do not know.

---

## Comment 6

> Username: swatanabe  
> Created at: 2019-03-02 23:06:19 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468969138  

AMDG  
  
On 3/2/19 3:47 PM, Peter Dimov wrote:  
> I however wonder about two things: one, whether warnings=on should still be -Wall for gcc, or the default; and two, whether we could somehow make it warnings=pedantic fall back to =extra, and =extra fall back to =all, to avoid fixing all toolsets to recognize the new values.  
>   
  
It's not possible without a major rewrite of feature handling.  
  
> I remember that there was a syntax `extra : all` when defining a feature that did that, but I may be misremembering.  
>   
  
No.  I suspect that you're thinking of the syntax  
for defining a new value of a composite feature.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-03-02 23:11:18 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468969430  

Is MSVC [`/Wall`](https://docs.microsoft.com/en-us/cpp/preprocessor/compiler-warnings-that-are-off-by-default?view=vs-2017) of any use? I opened #402 but I cannot decide which flags are preferable.

---

## Comment 8

> Username: pdimov  
> Created at: 2019-03-02 23:29:04 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468970490  

No. It's really /Wall, the equivalent of Clang's -Weverything. Completely useless. Warns if you wear a hat, warns if you don't wear a hat.

---

## Comment 9

> Username: Kojoley  
> Created at: 2019-03-02 23:44:24 UTC  
> Url: https://github.com/boostorg/build/issues/394#issuecomment-468971392  

Looks like the warnings under `/Wall` are mostly those that were disabled since VS2015 so they will still be emitted by build with previous VS, and few of them are actually useful e.g.:  
  - [C4062](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-4-c4062?view=vs-2017) enumerator 'identifier' in a switch of enum 'enumeration' is not handled  
  - [C4265](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-3-c4265?view=vs-2017) 'class': class has virtual functions, but destructor is not virtual
