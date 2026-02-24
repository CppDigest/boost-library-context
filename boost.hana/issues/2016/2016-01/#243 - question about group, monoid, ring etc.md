# #243 - question about group, monoid, ring etc [Closed]

> Username: robertramey  
> Created at: 2016-01-22 06:51:05 UTC  
> Updated at: 2016-01-22 12:48:50 UTC  
> Closed at: 2016-01-22 12:48:46 UTC  
> Url: https://github.com/boostorg/hana/issues/243  

Looking through the documentation carefully for the first time it's starting to make some sense for me.  
  
In the reference - Concepts - I find group, mooned, ring etc.  But searching through the documentation I don't find anywhere where these concepts are actually used.  It's quite possible that they are used somewhere but I haven't found them - if so , how about a pointer.  On the other hand, if they are not actually used - what is the purpose of having them here?  
  
Robert Ramey

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-01-22 08:20:52 UTC  
> Updated at: 2016-01-22 08:21:18 UTC  
> Url: https://github.com/boostorg/hana/issues/243#issuecomment-173841009  

Under `Reference documentation -> Data Types`, each data type has a section called   
"Modeled Concepts".

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-22 12:48:46 UTC  
> Url: https://github.com/boostorg/hana/issues/243#issuecomment-173911328  

^ What he said. You'll find for example `hana::integral_constant`, which models everything from `Monoid` to `EuclideanRing`.
