# #860 - vectorized lower_bound [Open]

> Username: HelliceSaouli  
> Created at: 2021-02-24 18:15:00 UTC  
> Updated at: 2022-08-30 19:06:35 UTC  
> Url: https://github.com/boostorg/compute/issues/860  

Hello  
I have 2 sorted vectors(a, b) and i would like to do some binary search for each element in b i would like to have a lower_bound from a. this is called vectorized binary search which is possible in thrust, but i can't find it in boost

---

## Comment 1

> Username: chetanpandey1266  
> Created at: 2022-08-30 18:44:15 UTC  
> Updated at: 2022-08-30 18:44:33 UTC  
> Url: https://github.com/boostorg/compute/issues/860#issuecomment-1232031582  

Are you talking about this [binary search](https://thrust.github.io/doc/group__vectorized__binary__search_gac35a115642366fff80e85de54a9882c6.html#gac35a115642366fff80e85de54a9882c6)

---

## Comment 2

> Username: HelliceSaouli  
> Created at: 2022-08-30 19:06:35 UTC  
> Url: https://github.com/boostorg/compute/issues/860#issuecomment-1232054106  

yes, that one. I end up implementing my own brute force custom kernel for it.  but it would be nice to have boost version
