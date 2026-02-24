# #466 - Invalid requirements for insert in the documentation [Open]

> Username: vinipsmaker  
> Created at: 2020-05-23 23:37:18 UTC  
> Updated at: 2020-05-23 23:37:18 UTC  
> Url: https://github.com/boostorg/hana/issues/466  

[Documentation states](http://boostorg.github.io/hana/group__group-Sequence.html#gae22a1a184b1b2dd550fa4fa619bed2e9):  
  
>it must also be true that `n < length(xs)`  
  
So if I have a sequence of size 2, the only valid indexes are 0 and 1 then I wouldn't be able to insert a new element at all valid places. Insert means insert before, so it should be valid to insert before the end iterator (i.e. `length(xs)`) and the actual requirement should be:  
  
>it must also be true that `n <= length(xs)`  
  
I haven't fully tested, but the compiler accepts `n=length(xs)`, so for now I'm assuming that only the documentation is outdated.  
  
Thank you for the excellent library.
