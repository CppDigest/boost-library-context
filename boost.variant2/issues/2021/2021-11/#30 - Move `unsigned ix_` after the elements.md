# #30 - Move `unsigned ix_` after the elements... [Closed]

> Username: pdimov  
> Created at: 2021-11-03 14:59:20 UTC  
> Updated at: 2021-12-09 23:18:03 UTC  
> Closed at: 2021-12-09 23:18:03 UTC  
> Url: https://github.com/boostorg/variant2/issues/30  

... to enable tail calls without the need to adjust `rdi` as in https://godbolt.org/z/jGMoMzT3E.
