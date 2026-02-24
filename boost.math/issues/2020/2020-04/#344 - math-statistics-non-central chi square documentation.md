# #344 - math/statistics/non-central chi square documentation [Closed]

> Username: nbecker  
> Created at: 2020-04-17 11:10:59 UTC  
> Updated at: 2020-06-11 19:21:59 UTC  
> Closed at: 2020-06-11 19:21:59 UTC  
> Url: https://github.com/boostorg/math/issues/344  

The description of the pdf in  
https://www.boost.org/doc/libs/1_72_0/libs/math/doc/html/math_toolkit/dist_ref/dists/nc_chi_squared_dist.html  
  
mixes 2 symbols, k and v.  There should only be one of them: k and v are the same (the degrees of freedom).  c.f., https://en.wikipedia.org/wiki/Noncentral_chi-squared_distribution

---

## Comment 1

> Username: ambreshbiradar9  
> Created at: 2020-06-09 06:52:20 UTC  
> Updated at: 2020-06-09 06:53:02 UTC  
> Url: https://github.com/boostorg/math/issues/344#issuecomment-641071764  

Hi @nbecker,  
can I convert k to v?  
I am new to git and opensource   
v= degrees of freedom

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-06-09 13:07:48 UTC  
> Url: https://github.com/boostorg/math/issues/344#issuecomment-641278945  

@ambreshbiradar9 : Yes, try [this file](https://github.com/boostorg/math/blob/develop/doc/distributions/nc_chi_squared.qbk).

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-06-09 17:57:23 UTC  
> Url: https://github.com/boostorg/math/issues/344#issuecomment-641477557  

Sorry we weren't clearer - we need to fix the docs - but basically k and v are just different names for the degree of freedom parameter, and whichever we use we meant the same thing.

---

## Comment 4

> Username: ambreshbiradar9  
> Created at: 2020-06-09 23:35:44 UTC  
> Url: https://github.com/boostorg/math/issues/344#issuecomment-641634984  

@NAThompson   
  
please forgive me I am new to this   
I did not understand how to work on your given file link  
  
I am changing k to v  
can i change the html file from the path :  
libs/math/doc/html/math_toolkit/dist_ref/dists/nc_chi_squared_dist.html  
and also corresponding equation files as well like from  
boost/libs/math/doc/equations/nc_chi_squ_ref1.svg

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-06-10 00:05:09 UTC  
> Url: https://github.com/boostorg/math/issues/344#issuecomment-641642784  

The html file is generated from the `.qbk`. For the svg, you'll need to regenerate it using this: https://viereck.ch/latex-to-svg/

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-06-10 00:08:07 UTC  
> Url: https://github.com/boostorg/math/issues/344#issuecomment-641643671  

@ambreshbiradar9 : Fork this repo, create a draft PR, and I'll help you out.

---

## Comment 7

> Username: NAThompson  
> Created at: 2020-06-11 19:21:59 UTC  
> Url: https://github.com/boostorg/math/issues/344#issuecomment-642881349  

Fixed in #367
