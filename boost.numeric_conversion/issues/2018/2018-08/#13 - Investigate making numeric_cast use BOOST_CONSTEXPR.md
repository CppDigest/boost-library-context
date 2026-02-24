# #13 - Investigate making numeric_cast use BOOST_CONSTEXPR. [Closed]

> Username: brandon-kohn  
> Created at: 2018-08-15 18:27:24 UTC  
> Updated at: 2024-11-22 10:53:55 UTC  
> Closed at: 2019-12-31 15:04:56 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/13  

TODO: I can't think of any reasons off the top of my head why this wouldn't work. I use numeric_cast to perform scaling conversions for a fixed_point type. This is required for the constructor of the fixed_point to be constexpr.

---

## Comment 1

> Username: tobiasleibner  
> Created at: 2024-11-22 10:53:54 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/13#issuecomment-2493473021  

This has been closed as completed but there is no `constexpr` version of `numeric_cast`, is there? Do you remember what the outcome of this investigation was?
