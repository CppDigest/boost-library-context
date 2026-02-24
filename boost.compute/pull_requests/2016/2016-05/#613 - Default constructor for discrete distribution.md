# #613 Default constructor for discrete distribution [Merged]

> Username: jszuppe  
> Created at: 2016-05-10 22:38:01 UTC  
> Updated at: 2016-05-31 22:23:16 UTC  
> Merged at: 2016-05-13 03:23:25 UTC  
> Closed at: 2016-05-13 03:23:25 UTC  
> Url: https://github.com/boostorg/compute/pull/613  

Resolves https://github.com/boostorg/compute/issues/612. I've also added `min()` and `max()` methods and changed `boost::compute::discrete_distribution` so now it generates `IntType` values instead of always generating `uint` values.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-05-10 23:44:06 UTC  
> Url: https://github.com/boostorg/compute/pull/613#issuecomment-218324288  

[![Coverage Status](https://coveralls.io/builds/6128073/badge)](https://coveralls.io/builds/6128073)  
  
Coverage increased (+0.07%) to 88.874% when pulling **02b444d56b03d96dba0efb3d67f264b3014f5d27 on haahh:pr_discrete_distribution** into **d172c8020e32f701812334dfdfd4263babb9a98e on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-05-11 05:10:22 UTC  
> Updated_at: 2016-05-11 06:32:33 UTC  
> Url: https://github.com/boostorg/compute/pull/613#discussion_r62792604  

I think the `BOOST_PREVENT_MACRO_SUBSTITUTION` macro needs to go between the function name and the parentheses. Something like:  
  
```  
result_type min BOOST_PREVENT_MACRO_SUBSTITUTION () const  
```

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-05-11 06:35:17 UTC  
> Url: https://github.com/boostorg/compute/pull/613#discussion_r62797598  

Thanks! Fixed!

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-05-11 09:28:18 UTC  
> Url: https://github.com/boostorg/compute/pull/613#issuecomment-218408081  

[![Coverage Status](https://coveralls.io/builds/6132661/badge)](https://coveralls.io/builds/6132661)  
  
Changes Unknown when pulling **56ad192b92ad227603ff158a77e7d07320d6c91f on haahh:pr_discrete_distribution** into *\* on boostorg:develop**.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2016-05-13 03:23:26 UTC  
> Url: https://github.com/boostorg/compute/pull/613#issuecomment-218943181  

Looks good, merged!

---
