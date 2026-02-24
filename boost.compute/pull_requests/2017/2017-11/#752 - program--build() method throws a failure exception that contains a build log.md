# #752 program::build() method throws a failure exception that contains a build log [Merged]

> Username: Kristian-Popov  
> Created at: 2017-11-15 13:27:30 UTC  
> Updated at: 2017-11-29 09:24:50 UTC  
> Merged at: 2017-11-29 09:24:50 UTC  
> Closed at: 2017-11-29 09:24:50 UTC  
> Url: https://github.com/boostorg/compute/pull/752  

Good day!  
I've prepared changes to change an exception type thrown by program::build() when program build fails (discussion can be found [here](https://github.com/boostorg/compute/issues/750))  
There's one question left - whom should I mention in copyright notices in new files?  
  
Thanks for your time,  
Kristian Popov

---

## Comment 1

> Username: jszuppe  
> Created_at: 2017-11-15 13:37:34 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-344594302  

> There's one question left - whom should I mention in copyright notices in new files?  
  
You should mention yourself.

---

## Comment 2

> Username: coveralls  
> Created_at: 2017-11-15 14:14:19 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-344604274  

[![Coverage Status](https://coveralls.io/builds/14214801/badge)](https://coveralls.io/builds/14214801)  
  
Coverage increased (+0.03%) to 83.795% when pulling **5ecf9fd7e31fb5844aa845fe66044af48331d8ee on Kristian-Popov:program-build-failure-exception** into **7daca6fc1ff289022c46fc6466565586f68f2244 on boostorg:develop**.

---

## Review 3 [Changes requested]

> Username: jszuppe  
> Created_at: 2017-11-16 22:26:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/compute/pull/752#pullrequestreview-77283687  

See the comment in code, otherwise looks ok.

📁 include/boost/compute/exception/program_build_failure.hpp

```diff
  28 |+     {
  29 |+     }
  30 |+ 
```

> Username: jszuppe  
> Created_at: 2017-11-16 22:21:14 UTC  
> Updated_at: 2017-11-28 19:53:53 UTC  
> Url: https://github.com/boostorg/compute/pull/752#discussion_r151554219  

You need to explicitly add empty destructor with `throw()` specifier to make it `noexcept`. Destructors are implicitly `noexcept` since C++11.


---

## Comment 4

> Username: coveralls  
> Created_at: 2017-11-21 21:40:37 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-346170071  

[![Coverage Status](https://coveralls.io/builds/14312842/badge)](https://coveralls.io/builds/14312842)  
  
Coverage increased (+0.03%) to 83.801% when pulling **96caffb3d814f4f692bec4e6d8a8061cb1573f61 on Kristian-Popov:program-build-failure-exception** into **7daca6fc1ff289022c46fc6466565586f68f2244 on boostorg:develop**.

---

## Comment 5

> Username: coveralls  
> Created_at: 2017-11-22 16:36:18 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-346405151  

[![Coverage Status](https://coveralls.io/builds/14326991/badge)](https://coveralls.io/builds/14326991)  
  
Coverage increased (+0.03%) to 83.801% when pulling **f17e4cff1ff491d0289a103e543c678a9772ea5a on Kristian-Popov:program-build-failure-exception** into **ce38fb7b95106470e1662e317281cd48faf7373f on boostorg:develop**.

---

## Comment 6

> Username: jszuppe  
> Created_at: 2017-11-22 17:03:50 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-346413398  

I'm not sure why it fails on POCL. Maybe it's the 2nd build on a program that failed? Also please remove the merge commit for `develop` branch. If you need something from `develop` branch please just rebase your commit on it.

---

## Comment 7

> Username: jszuppe  
> Created_at: 2017-11-23 20:04:06 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-346690850  

Personally, I'd recommend to place destructor after constructors and assign operators, but that's not hard requirement.   
  
I won't merge before all tests on Travis CI are passing and before you remove https://github.com/boostorg/compute/pull/752/commits/f17e4cff1ff491d0289a103e543c678a9772ea5a commit. If I have some free time, I'll look into POCL case on Saturday,

---

## Comment 8

> Username: jszuppe  
> Created_at: 2017-11-25 22:45:43 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-346971203  

It seems that POCL fails on that 2nd build of the program with incorrect code. I'll verify that on the POCL's `master`, open an issue. If they confirm the bug I'll implement workaround for test and commit it.

---

## Comment 9

> Username: Kristian-Popov  
> Created_at: 2017-11-26 14:59:28 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-347014200  

Thank you for taking a lead investigating a failure.  
I removed faulty merge of `develop` branch (sorry about that, still not very good at git). Glad to say you localized an issue, I finally built test suite on Ubuntu VM, but I get many critical errors like heap corruption, invalid memory deallocation etc., so I won't be able to help investigating POCL issue anytime soon.

---

## Comment 10

> Username: jszuppe  
> Created_at: 2017-11-28 19:57:14 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-347644857  

Bug in POCL confirmed: https://github.com/pocl/pocl/issues/577. I added workaround for that in tests and did a few cosmetic changes to your commits. When CI confirms it works, I'll merge it.

---

## Comment 11

> Username: coveralls  
> Created_at: 2017-11-29 09:09:37 UTC  
> Url: https://github.com/boostorg/compute/pull/752#issuecomment-347797145  

[![Coverage Status](https://coveralls.io/builds/14415532/badge)](https://coveralls.io/builds/14415532)  
  
Coverage increased (+0.03%) to 83.801% when pulling **a3c41268600adb4e96424ea16369c9885ac1b54e on Kristian-Popov:program-build-failure-exception** into **ce38fb7b95106470e1662e317281cd48faf7373f on boostorg:develop**.

---
