# #68 - BOOST_INLINE_VARIABLE does not name a type [Closed]

> Username: NAThompson  
> Created at: 2019-12-31 02:25:36 UTC  
> Updated at: 2019-12-31 02:39:07 UTC  
> Closed at: 2019-12-31 02:39:07 UTC  
> Url: https://github.com/boostorg/core/issues/68  

On top of `boost/core` commit:  
  
```  
commit 72ca0d8664ebabbefaf00aba205570ebbc877beb (HEAD -> develop, origin/develop, origin/HEAD)  
Author: Peter Dimov <pdimov@gmail.com>  
Date:   Sat Dec 28 18:47:11 2019 +0200  
  
    Add CMake install support  
```  
  
and on top of `boost/multiprecision` commit  
  
```  
commit 632f68400c28e85d62171c40b16d71699ac6dfd7 (HEAD -> develop, origin/develop, origin/HEAD)  
Author: Christopher Kormanyos <ckormanyos@yahoo.com>  
Date:   Fri Dec 13 22:46:54 2019 +0100  
  
    Repair excessive right shift in rotr. Change benchmark units to kops/s.  
```  
  
we have the following problem in `boost.math`:  
  
```  
g++-9 -g --std=c++17 -ffast-math -fno-finite-math-only -march=native -Wfatal-errors -MMD -fvisibility=hidden -O3 -I../../ -I/usr/local/include -o example/daubechies_coefficients.x example/daubechies_coefficients.cpp -pthread -L/usr/local/lib  
In file included from ../../boost/multiprecision/cpp_int.hpp:15,  
                 from ../../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from example/daubechies_coefficients.cpp:13:  
../../boost/core/empty_value.hpp:142:1: error: 'BOOST_INLINE_VARIABLE' does not name a type  
  142 | BOOST_INLINE_VARIABLE  
      | ^~~~~~~~~~~~~~~~~~~~~  
compilation terminated due to -Wfatal-errors.  
```  
  
Boost.math commit (though it is, I think, largely irrelevant):  
  
```  
commit b3e1f4d8a11b3ca4329464a60ec726b5acea6ebe (HEAD -> develop, origin/develop, origin/HEAD)  
Merge: ce48f975e e5f019c62  
Author: Nick <NAThompson@users.noreply.github.com>  
Date:   Sat Dec 28 09:32:13 2019 -0500  
  
    Merge pull request #290 from boostorg/distribution_entropy  
  
    Entropy of distributions.  
```  
  
`git bisect` shows the offending commit:  
  
```  
git bisect bad  
38f71361fbbba1e8bf23876e218e8622e6cbc406 is the first bad commit  
commit 38f71361fbbba1e8bf23876e218e8622e6cbc406  
Author: Andrey Semashev <Lastique@users.noreply.github.com>  
Date:   Sun Nov 24 05:00:59 2019 +0300  
  
    Add a convenience instance of empty_init_t (#65)  
  
    * Added a convenience instance of empty_init_t.  
  
    Closes https://github.com/boostorg/core/issues/63.  
  
:040000 040000 b06283a1bec80f56b9a76e7a872aa5631863f487 719b9902bfadfa5ba19ce86c480373794b50aa9e M	include  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-31 02:36:16 UTC  
> Url: https://github.com/boostorg/core/issues/68#issuecomment-569850832  

What Boost.Config are you using? The latest develop and master both have BOOST_INLINE_VARIABLE.

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-12-31 02:39:07 UTC  
> Url: https://github.com/boostorg/core/issues/68#issuecomment-569851085  

@glenfe : My bad; that was it. I updated to boost.config master and this was resolved.
