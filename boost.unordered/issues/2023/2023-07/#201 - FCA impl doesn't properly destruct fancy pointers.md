# #201 - FCA impl doesn't properly destruct fancy pointers [Closed]

> Username: cmazakas  
> Created at: 2023-07-27 15:32:06 UTC  
> Updated at: 2023-10-04 15:38:35 UTC  
> Closed at: 2023-10-04 15:38:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/201  

https://godbolt.org/z/3ds41hrrT  
  
We have a fancy pointer leak most likely caused by the internal hashing impl.
