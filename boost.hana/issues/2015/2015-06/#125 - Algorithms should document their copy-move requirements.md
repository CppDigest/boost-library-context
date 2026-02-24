# #125 - Algorithms should document their copy/move requirements [Open]

> Username: ldionne  
> Created at: 2015-06-16 13:27:03 UTC  
> Updated at: 2015-06-16 13:27:03 UTC  
> Url: https://github.com/boostorg/hana/issues/125  

For example `filter` does a copy of the sequence it takes. It should document the fact that the elements must be copy/move constructible.
