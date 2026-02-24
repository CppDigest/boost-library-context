# #63 Add coveralls.io integration [Merged]

> Username: kylelutz  
> Created at: 2014-03-12 05:26:29 UTC  
> Updated at: 2014-06-26 02:33:26 UTC  
> Merged at: 2014-03-13 02:06:41 UTC  
> Closed at: 2014-03-13 02:06:41 UTC  
> Url: https://github.com/boostorg/compute/pull/63  

This adds support for automatic code-coverage generation with  
coveralls.io and Travis-CI.

---

## Comment 1

> Username: ddemidov  
> Created_at: 2014-03-12 05:46:57 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37377569  

Cool! There is however a problem with testing coverage of a header only library: templates that are not used, are not instantiated, and the coverage utility does not know about them. Hence, one always gets almost 100% coverage :).

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-03-12 05:51:51 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37377778  

Hopefully it will still give some useful information and, in any case, 100% coverage still looks good :-).  
  
Unfortunately, I haven't had any luck getting coverage data to show up at https://coveralls.io/r/kylelutz/compute. Have you tried this before? Perhaps it's something on their end, none of the branches are showing up.

---

## Comment 3

> Username: ddemidov  
> Created_at: 2014-03-12 05:53:27 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37377855  

No, that's first time I actually heard about coveralls.io. Was waiting for you to finish with the setup to get it easy :).

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-03-12 06:09:03 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37378449  

Submitted an issue to the coveralls issue-tracker. Hopefully this will get figured out.

---

## Comment 5

> Username: coveralls  
> Created_at: 2014-03-12 07:35:12 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37382118  

[![Coverage Status](https://coveralls.io/builds/590623/badge)](https://coveralls.io/builds/590623)  
  
Changes Unknown when pulling **ebfd339a19c54b2edd755005fc324eef071eab01 on coveralls** into *\* on develop**.

---

## Comment 6

> Username: coveralls  
> Created_at: 2014-03-12 17:52:07 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37440938  

[![Coverage Status](https://coveralls.io/builds/591999/badge)](https://coveralls.io/builds/591999)  
  
Changes Unknown when pulling **19af921c7ddc57dd7b742e840e867ded5fca8201 on coveralls** into *\* on develop**.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-03-13 01:29:40 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37490776  

Coverage finally came back. Looks like it just takes a while...  
  
This probably has to be tweaked some more in the future to get better coverage numbers but I'm going to merge it in. Should be fairly easy to pull into VexCL as well.

---

## Comment 8

> Username: ddemidov  
> Created_at: 2014-03-13 06:54:46 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37504939  

I've pulled the changes into VexCL, thanks! Too bad they are down for maintenance..

---

## Comment 9

> Username: coveralls  
> Created_at: 2014-03-13 23:40:14 UTC  
> Url: https://github.com/boostorg/compute/pull/63#issuecomment-37600594  

[![Coverage Status](https://coveralls.io/builds/593844/badge)](https://coveralls.io/builds/593844)  
  
Changes Unknown when pulling **524ce5c7991008105dd22ae1a15fca9c006cf398 on coveralls** into *\* on develop**.

---
