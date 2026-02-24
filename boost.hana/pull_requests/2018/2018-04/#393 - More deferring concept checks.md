# #393 More deferring concept checks [Merged]

> Username: ricejasonf  
> Created at: 2018-04-12 06:15:28 UTC  
> Updated at: 2018-04-14 13:25:53 UTC  
> Merged at: 2018-04-14 13:25:45 UTC  
> Closed at: 2018-04-14 13:25:45 UTC  
> Url: https://github.com/boostorg/hana/pull/393  

I encountered this chicken/egg thing again with `sum<Tag>` so I hit them all to be consistent.  
  
:sweat_smile:

---

## Review 1 [Commented]

> Username: ldionne  
> Created_at: 2018-04-12 07:57:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/393#pullrequestreview-111514021  

📁 include/boost/hana/sum.hpp

```diff
  38 |+         );
  39 | 
  40 |-         #ifndef BOOST_HANA_CONFIG_DISABLE_CONCEPT_CHECKS
```

> Username: ldionne  
> Created_at: 2018-04-12 07:57:58 UTC  
> Updated_at: 2018-04-13 15:55:38 UTC  
> Url: https://github.com/boostorg/hana/pull/393#discussion_r180993961  

Could you please group the two concept checks inside the same `#ifndef` please? Here and elsewhere.

> Username: ldionne  
> Created_at: 2018-04-13 13:02:19 UTC  
> Updated_at: 2018-04-13 15:55:38 UTC  
> Url: https://github.com/boostorg/hana/pull/393#discussion_r181380156  

Ping. I'm good to merge this once this is done. I'd like to avoid having multiple `#ifdefs` for `BOOST_HANA_CONFIG_DISABLE_CONCEPT_CHECKS` in the same function when that makes sense.

> Username: ricejasonf  
> Created_at: 2018-04-13 16:10:06 UTC  
> Url: https://github.com/boostorg/hana/pull/393#discussion_r181437469  

I had pushed for this, but apparently I missed the second overloads for `monadic_fold_left/right`.  
I rechecked them all and pushed again. Should be fixed now. :sweat_smile:


---

## Comment 2

> Username: ricejasonf  
> Created_at: 2018-04-12 15:49:13 UTC  
> Url: https://github.com/boostorg/hana/pull/393#issuecomment-380853115  

Err.. I did two force pushes so maybe the checks for bb412ea can be stopped.

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2018-04-12 22:34:42 UTC  
> Url: https://github.com/boostorg/hana/pull/393#issuecomment-380964888  

It looks like the documentation test failed, but this time it wasn't my fault :stuck_out_tongue:   
> The program 'doxygen' is currently not installed. To run 'doxygen' please ask your administrator to install the package 'doxygen'  
  
FWIW I ran `make doc` on my system and it doens't have any warnings.  
```  
root@256fd253009f:/opt/build# make doc  
Scanning dependencies of target doc  
Generating API documentation with Doxygen  
Built target doc  
root@256fd253009f:/opt/build#  
```

---

## Comment 4

> Username: ldionne  
> Created_at: 2018-04-14 13:25:53 UTC  
> Url: https://github.com/boostorg/hana/pull/393#issuecomment-381329120  

Thank you!

---
