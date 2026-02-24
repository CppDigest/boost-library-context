# #19 Merge added dimensions and units [Open]

> Username: mwpowellhtx  
> Created at: 2015-08-11 13:06:11 UTC  
> Updated at: 2016-08-12 09:37:52 UTC  
> Url: https://github.com/boostorg/units/pull/19  

See fork issues [#1](http://github.com/mwpowellhtx/units/issues/1), [#2](http://github.com/mwpowellhtx/units/issues/2), and [#3](http://github.com/mwpowellhtx/units/issues/3). Definitely interested in some feedback on how to better approach number two. Could forego number three provided there was a better approach. Thanks and let me know how the merge went.

---

## Comment 1

> Username: mkurdej  
> Created_at: 2015-08-15 14:11:33 UTC  
> Url: https://github.com/boostorg/units/pull/19#discussion_r37136185  

Maybe you should add singular version of kelvins as well? I.e. joule_per_kilogram_kelvin and 3 other variants.

---

## Comment 2

> Username: mkurdej  
> Created_at: 2015-08-15 14:12:10 UTC  
> Url: https://github.com/boostorg/units/pull/19#issuecomment-131385602  

And what about tests?

---

## Comment 3

> Username: mwpowellhtx  
> Created_at: 2015-08-15 14:42:57 UTC  
> Url: https://github.com/boostorg/units/pull/19#discussion_r37136393  

Certainly, if I have a need for it, I will patch it. Other's contributions are also welcomed.

---

## Comment 4

> Username: mkurdej  
> Created_at: 2015-08-15 15:54:05 UTC  
> Url: https://github.com/boostorg/units/pull/19#discussion_r37136896  

It's not a question of one's need, but of consistency...

---

## Comment 5

> Username: mwpowellhtx  
> Created_at: 2015-08-15 17:21:57 UTC  
> Url: https://github.com/boostorg/units/pull/19#discussion_r37137482  

The harder question is how to persuade the units system to accept a true energy (Joules) per mass (kilogram) temperature (kelvins). The closest I could come up with was to "manually" reduce the dimensions, which will work when you involve the quantity calculations, but does not appear to be technically correct, according to the Specific Gas Constant docs I was reading. I don't have any ready answers where that's concerned. Can you help with those bits?

---

## Comment 6

> Username: jhunold  
> Created_at: 2016-08-12 09:37:52 UTC  
> Url: https://github.com/boostorg/units/pull/19#issuecomment-239403955  

Won't merge without tests. And please make a pull request against develop, not master. Thanks.

---
