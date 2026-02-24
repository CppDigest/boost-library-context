# #20 turned histogram python into proper pyd [Merged]

> Username: klemens-morgenstern  
> Created at: 2016-06-20 20:23:23 UTC  
> Updated at: 2016-06-23 00:21:35 UTC  
> Merged at: 2016-06-23 00:21:35 UTC  
> Closed at: 2016-06-23 00:21:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/20  

Damit da auch eine .pyd rauspurzelt und keine lib.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-06-20 20:26:05 UTC  
> Url: https://github.com/boostorg/histogram/pull/20#issuecomment-227258743  

[![Coverage Status](https://coveralls.io/builds/6676048/badge)](https://coveralls.io/builds/6676048)  
  
Coverage remained the same at 95.906% when pulling **e83c215ade8b09809e09c6a5c0b1294a21208ed0 on klemens-morgenstern:develop** into **922ed14e8a72d53383946ba94b66b6feffd6086c on HDembinski:master**.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2016-06-20 21:21:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/20#issuecomment-227273478  

Alright, I added the Iterator-version with disable if, and thus removed boost::iterator_range. Should be a seperate PR, but I think that is a small change enough to be here.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2016-06-20 21:26:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/20#issuecomment-227274764  

The enable_if header moved between boost 1.55 and 1.61, that's why it won't work. I don't wanna fiddle around with your travis.yml, so maybe you could modify that to get a newer version (1.56 at the least) and then it will work. If you do so, I'll pull this into my branch and then we'll see if it works.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2016-06-20 21:37:06 UTC  
> Url: https://github.com/boostorg/histogram/pull/20#issuecomment-227277357  

travis is a bit behind with boost, unfortunately. they provide a way to pull in a precompiled boost1.55 package, but nothing more recent than that. We can decide to drop using the boost package, and compile boost ourselves, then there is no problem in using the latest version. Using the package is convenient because it cuts down the compilation time, but it not necessary.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2016-06-20 21:40:10 UTC  
> Url: https://github.com/boostorg/histogram/pull/20#issuecomment-227278127  

I know, funny enough that this is one version too early. The official boost library (and boost.process) pull the complete boost from github, so you know that the whole things works on the develop branch with all the other libraries before a release. But that causes horrible compile times.  
  
Now, if I'd be able to get the python tests to run, the boost.build would automatically only compile the libraries you actually need, not all of it.

---
