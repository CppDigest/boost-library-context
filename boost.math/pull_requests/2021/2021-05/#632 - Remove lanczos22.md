# #632 Remove lanczos22. [Merged]

> Username: jzmaddock  
> Created at: 2021-05-20 11:50:57 UTC  
> Updated at: 2024-01-24 21:34:38 UTC  
> Merged at: 2021-05-21 07:43:49 UTC  
> Closed at: 2021-05-21 07:43:49 UTC  
> Url: https://github.com/boostorg/math/pull/632  

It was only used for 34 and 35 digit counts and had terrible accuracy at fixed precision.  
See https://github.com/boostorg/multiprecision/pull/326#issuecomment-844997628.  
Fortunately the "MP" suffixed traits appear fine, though further testing inside Multiprecision will follow.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-05-20 12:22:00 UTC  
> Updated_at: 2021-05-20 12:22:48 UTC  
> Url: https://github.com/boostorg/math/pull/632#issuecomment-845053845  

This is cool.  
  
I'm not sure, but I wonder @jzmaddock if we remove `lanczos22`, does that influence multiprecision's specialization, which references `lanczos22` by name [here](https://github.com/boostorg/multiprecision/blob/bb261007705b509db6b81e54127383e96abd1878/include/boost/multiprecision/detail/big_lanczos.hpp#L29)?  
  
Do you think it might ultimately be possible to reduce that specialization in multiprecision altogether?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-05-20 12:26:53 UTC  
> Url: https://github.com/boostorg/math/pull/632#issuecomment-845058422  

>I'm not sure, but I wonder @jzmaddock if we remove lanczos22, does that influence multiprecision's specialization, which references lanczos22 by name here?  
  
Different type.  
  
>Do you think it might ultimately be possible to reduce that specialization in multiprecision altogether?  
  
I think we should be able to remove altogether yes, but lets get the change in Math develop and bash the tests into better shape first.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-05-21 07:43:42 UTC  
> Url: https://github.com/boostorg/math/pull/632#issuecomment-845735122  

One failure is a network issue, so merging.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-05-21 09:35:09 UTC  
> Url: https://github.com/boostorg/math/pull/632#issuecomment-845822426  

> I think we should be able to remove altogether yes, but lets get the change in Math develop and bash the tests into better shape first.  
  
Understood. This is moving along very well and quickly. Thanks John.

---
