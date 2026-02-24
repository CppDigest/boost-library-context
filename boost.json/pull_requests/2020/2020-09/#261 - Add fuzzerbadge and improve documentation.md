# #261 Add fuzzerbadge and improve documentation [Closed]

> Username: pauldreik  
> Created at: 2020-09-03 18:07:52 UTC  
> Updated at: 2020-09-04 23:35:43 UTC  
> Closed at: 2020-09-04 23:35:43 UTC  
> Url: https://github.com/boostorg/json/pull/261  

Added a fuzzer badge, Closes #237. I did not add a link for master, since that seems to not get updated by the cron job and I do not want to give the false impression that master is fuzzed.  
  
Also, I saw a recent crash detected by the fuzzer job so I minimized it and documented how I did it.

---

## Comment 1

> Username: pauldreik  
> Created_at: 2020-09-03 18:15:59 UTC  
> Url: https://github.com/boostorg/json/pull/261#issuecomment-686664516  

The crash was introduced in https://github.com/CPPAlliance/json/commit/c4dc2b2052e264a97e8e512ae2a5ce83814b797e  
  
The CI job caught it.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-09-04 17:21:21 UTC  
> Url: https://github.com/boostorg/json/pull/261#issuecomment-687280420  

This has conflicts and needs rebasing

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-09-04 17:21:31 UTC  
> Url: https://github.com/boostorg/json/pull/261#issuecomment-687280493  

Can we fuzz master too?

---

## Comment 4

> Username: pauldreik  
> Created_at: 2020-09-04 20:02:48 UTC  
> Url: https://github.com/boostorg/json/pull/261#issuecomment-687354475  

> Can we fuzz master too?  
  
I guess, but that would need some github action magic I don't know how to do even if I am sure it's easy.  
  
Worse is, there is a risk of something I call corpus ping-pong. If master lags develop or the branches diverge, one will destroy the corpus for the other. Then we need to separate the corpora and that needs more of that unpleasant github action - bash script - bintray debugging. Of course it can be done, I just don't feel like it is worth the effort right now. I would rather wait until (if?) this library gets accepted into boost, and then get it onto oss fuzz instead which has a much more professional setup.

---

## Comment 5

> Username: pauldreik  
> Created_at: 2020-09-04 20:10:47 UTC  
> Url: https://github.com/boostorg/json/pull/261#issuecomment-687358155  

> This has conflicts and needs rebasing  
  
fixed

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2020-09-04 20:47:44 UTC  
> Url: https://github.com/boostorg/json/pull/261#issuecomment-687375117  

corpus ping-pong... lmao. OK

---
