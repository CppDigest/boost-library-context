# #367 i tried removing  non-essential  "/ " symbols [Merged]

> Username: ambreshbiradar9  
> Created at: 2020-06-10 00:57:23 UTC  
> Updated at: 2020-06-12 12:58:19 UTC  
> Merged at: 2020-06-11 19:19:35 UTC  
> Closed at: 2020-06-11 19:19:36 UTC  
> Url: https://github.com/boostorg/math/pull/367  



---

## Comment 1

> Username: ambreshbiradar9  
> Created_at: 2020-06-10 00:59:22 UTC  
> Url: https://github.com/boostorg/math/pull/367#issuecomment-641657549  

@NAThompson hi created this

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2020-06-10 01:11:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/367#pullrequestreview-427645920  

📁 doc/distributions/nc_chi_squared.qbk

```diff
  74 | 
  75 |- Constructs a Chi-Squared distribution with /v/ degrees of freedom
  75 |+ Constructs a Chi-Squared distribution with v degrees of freedom
```

> Username: NAThompson  
> Created_at: 2020-06-10 01:11:16 UTC  
> Updated_at: 2020-06-10 04:34:01 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r437805257  

This one is incorrect, the /v/ will put it in italics.

> Username: NAThompson  
> Created_at: 2020-06-10 02:05:43 UTC  
> Updated_at: 2020-06-10 04:34:01 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r437818925  

I would replace it by [nu]

> Username: ambreshbiradar9  
> Created_at: 2020-06-10 03:39:49 UTC  
> Updated_at: 2020-06-12 02:36:35 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r437841994  

I have made the change.   
but why do we have to write [nu] in square brackets  
in the latex link, you sent earlier  \nu  will give the correct symbol  
  
you can point me to some resource link  
@NAThompson


---

## Review 3 [Commented]

> Username: ambreshbiradar9  
> Created_at: 2020-06-10 04:38:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/367#pullrequestreview-427705211  

📁 doc/distributions/nc_chi_squared.qbk

```diff
 235 |- Where ['f(x; v)] is the PDF of the central __chi_squared_distrib and
 235 |+ Where ['f(x;[nu])] is the PDF of the central __chi_squared_distrib and
 236 | ['I[sub v](x)] is a modified Bessel function, see __cyl_bessel_i.
```

> Username: ambreshbiradar9  
> Created_at: 2020-06-10 04:38:36 UTC  
> Updated_at: 2020-06-10 04:42:54 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r437855599  

@NAThompson  have doubt for the I function can be defined by other variables as it will avoid confusion with [nu]  
 I am not so sure please can you tell  
![image](https://user-images.githubusercontent.com/19732486/84227675-a4b79380-ab02-11ea-9a61-a574f69030ce.png)  
https://en.wikipedia.org/wiki/Noncentral_chi-squared_distribution

> Username: NAThompson  
> Created_at: 2020-06-11 19:16:32 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r439013360  

Yes, I see the issue. I'm going to merge your PR and I'll get this one.


---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-06-11 19:19:29 UTC  
> Url: https://github.com/boostorg/math/pull/367#issuecomment-642880216  

Looks like I pushed a bad commit, so your build failures are my fault.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-06-12 00:28:48 UTC  
> Url: https://github.com/boostorg/math/pull/367#issuecomment-642999566  

@ambreshbiradar9 : Want to try [this one](https://github.com/boostorg/multiprecision/issues/121) next? Hexadecimal printing needs to be implemented for the mpfr backend.

---

## Comment 6

> Username: ambreshbiradar9  
> Created_at: 2020-06-12 05:31:14 UTC  
> Url: https://github.com/boostorg/math/pull/367#issuecomment-643073999  

@NAThompson what about the equations ?

---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2020-06-12 09:04:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/367#pullrequestreview-429580909  

📁 doc/distributions/nc_chi_squared.qbk

```diff
  81 | 
  82 |- Returns the parameter /v/ from which this object was constructed.
  82 |+ Returns the parameter v from which this object was constructed.
```

> Username: jzmaddock  
> Created_at: 2020-06-12 09:04:51 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r439299974  

Should this be a [nu] as well?

> Username: ambreshbiradar9  
> Created_at: 2020-06-12 09:46:28 UTC  
> Updated_at: 2020-06-12 10:07:04 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r439319608  

ya i missed that one.  
 can i edit it again and make a pull request? @jzmaddock

> Username: NAThompson  
> Created_at: 2020-06-12 12:57:51 UTC  
> Updated_at: 2020-06-12 12:57:52 UTC  
> Url: https://github.com/boostorg/math/pull/367#discussion_r439402345  

@ambreshbiradar9 : Yes, edit again in a new PR.


---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-06-12 09:08:44 UTC  
> Url: https://github.com/boostorg/math/pull/367#issuecomment-643165189  

> what about the equations ?  
  
Originally the equations were written in MathML using MathCast and then converted to SVG's via a script.  The mml files are in git and can still be edited, but if you're familiar with tex then a better way might be to:  
  
* Use an online editor such as http://www.tlhiv.org/ltxpreview/ to author the corrected equations.  
* Download as an SVG and replace the existing SVG file.  
* Remove the mml file so we don't accidentally start from there in future.  
* "Archive" the tex used as a comment in the qbk file.  
  
Hope that makes sense - thanks for this!

---

## Comment 9

> Username: NAThompson  
> Created_at: 2020-06-12 12:57:32 UTC  
> Updated_at: 2020-06-12 12:58:19 UTC  
> Url: https://github.com/boostorg/math/pull/367#issuecomment-643256186  

@jzmaddock , @ambreshbiradar9 : I already fixed the equations on develop.

---
