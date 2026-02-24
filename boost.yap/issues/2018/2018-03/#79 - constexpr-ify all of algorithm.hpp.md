# #79 - constexpr-ify all of algorithm.hpp [Closed]

> Username: rgrover  
> Created at: 2018-03-10 01:38:23 UTC  
> Updated at: 2019-08-11 05:37:07 UTC  
> Closed at: 2019-08-11 05:37:07 UTC  
> Url: https://github.com/boostorg/yap/issues/79  

it should be possible to call transform on a constexpr expression using a constexpr xform to yield a constexpr value.

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-03-10 01:43:35 UTC  
> Url: https://github.com/boostorg/yap/issues/79#issuecomment-371992063  

This is true, but too limited.  I need to do a constexpr pass on all the algorithms.  Thanks for pointing this out.

---

## Comment 2

> Username: tzlaine  
> Created at: 2019-08-11 05:37:07 UTC  
> Url: https://github.com/boostorg/yap/issues/79#issuecomment-520201838  

This is now on develop, as of commit 884e7035cc27657cb60a7801422bcacf79d2f835
