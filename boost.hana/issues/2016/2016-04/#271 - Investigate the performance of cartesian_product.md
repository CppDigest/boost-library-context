# #271 - Investigate the performance of cartesian_product [Open]

> Username: ldionne  
> Created at: 2016-04-20 21:24:11 UTC  
> Updated at: 2016-04-20 21:24:11 UTC  
> Url: https://github.com/boostorg/hana/issues/271  

In a [recent discussion](https://gitter.im/mpark/variant?at=5716c55225b488663625639b) with @mpark, we found out that his implementation of multi visitation on variants was **much** faster than an equivalent implementation based on Hana (in terms of compile-time, we didn't check the runtime). This might hint at a performance problem in `hana::cartesian_product`, or even at a more general performance problem with constexpr-based computation of indices. This has to be looked into.
