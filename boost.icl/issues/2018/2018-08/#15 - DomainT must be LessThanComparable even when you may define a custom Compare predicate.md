# #15 - DomainT must be LessThanComparable even when you may define a custom Compare predicate. [Open]

> Username: brandon-kohn  
> Created at: 2018-08-17 18:29:16 UTC  
> Updated at: 2018-08-17 18:29:16 UTC  
> Url: https://github.com/boostorg/icl/issues/15  

It seems like that concept check narrows the allowable types too much. It would be nice to be able to use intervals of types who don't implement operator <.
