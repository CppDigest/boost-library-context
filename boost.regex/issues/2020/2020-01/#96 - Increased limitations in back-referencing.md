# #96 - Increased limitations in back-referencing [Closed]

> Username: JohnAlt2  
> Created at: 2020-01-13 18:00:09 UTC  
> Updated at: 2020-01-22 18:03:28 UTC  
> Closed at: 2020-01-22 18:03:27 UTC  
> Url: https://github.com/boostorg/regex/issues/96  

A 64bit specific change in behaviour has been introduced at some point after 1-56-0 that can be witnessed in 1-64-0.  
  
The following regex contains both the maximum number of relative back references "\g{-1}" (before Boost reports a syntax error in the expression, see Issue https://github.com/boostorg/regex/issues/75) and the maximum number of uniquely names back references.  
`(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(.)\g{-1}(?<a>.)\g{a}(?<b>.)\g{b}(?<c>.)\g{c}(?<d>.)\g{d}(?<e>.)\g{e}(?<f>.)\g{f}(?<g>.)\g{g}(?<h>.)\g{h}(?<i>.)\g{i}(?<j>.)\g{j}(?<aa>.)\g{aa}(?<ab>.)\g{ab}(?<ac>.)\g{ac}(?<ad>.)\g{ad}(?<ae>.)\g{ae}(?<af>.)\g{af}(?<ag>.)\g{ag}(?<ah>.)\g{ah}(?<ai>.)\g{ai}(?<aj>.)\g{aj}(?<ba>.)\g{ba}(?<bb>.)\g{bb}(?<bc>.)\g{bc}(?<bd>.)\g{bd}(?<be>.)\g{be}(?<bf>.)\g{bf}(?<bg>.)\g{bg}(?<bh>.)\g{bh}(?<bi>.)\g{bi}(?<bj>.)\g{bj}(?<ca>.)\g{ca}`  
  
This work fine for 1-56-0 (x32 & x64)  
This work fine for 1-64-0 32bit (Windows) build  
This fails on 1-64-0 64bit (Windows or Linux) build with a regex syntax error reported  
  
The new 64bit change in behaviour makes useless a potential fudge to Issue 75 to extend the number of back references from the 31 limit to a 62 limit. But obviously worrying to see any platform specific variations.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-19 11:23:04 UTC  
> Url: https://github.com/boostorg/regex/issues/96#issuecomment-575994746  

Has Github markup mangled that expression by any chance?  It contains some `(?.)` sequences which aren't valid regexes.

---

## Comment 2

> Username: JohnAlt2  
> Created at: 2020-01-20 10:35:42 UTC  
> Url: https://github.com/boostorg/regex/issues/96#issuecomment-576212083  

Yep, was mangled. Reinserted rexex in ticket description as literal code.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-01-22 18:03:27 UTC  
> Url: https://github.com/boostorg/regex/issues/96#issuecomment-577311478  

OK, that's working now in current develop (which is to say it appears to be the same issue as the last one).
