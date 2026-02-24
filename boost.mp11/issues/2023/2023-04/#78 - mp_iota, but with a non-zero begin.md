# #78 - mp_iota, but with a non-zero begin [Closed]

> Username: pdimov  
> Created at: 2023-04-28 17:05:46 UTC  
> Updated at: 2023-05-14 02:29:27 UTC  
> Closed at: 2023-05-14 02:29:27 UTC  
> Url: https://github.com/boostorg/mp11/issues/78  

mp_range_c<long long first, long long last>  
// or mp_irange_c (integral range)  
// pre: first < last  
  
mp_range<class First, class Last>  
// either decltype(First::value) == decltype(Last::value), or use  
// decltype(Last::value - First::value) as the common type and  
// check that First::value and Last::value can be converted to  
// it without narrowing  
// pre: T{First::value} < T{Last::value}

---

## Comment 1

> Username: pdimov  
> Created at: 2023-04-28 17:23:24 UTC  
> Url: https://github.com/boostorg/mp11/issues/78#issuecomment-1527869182  

mp_iota<N, First> is also good to have, though (size and first, instead of first and last). F.ex. this question  
  
https://stackoverflow.com/questions/76119083/transformation-of-compile-time-list-of-lists-of-types-into-a-list-of-lists-of-in/76130815  
  
can be solved like this  
  
https://godbolt.org/z/d7rPzsjsf  
  
and if mp_iota could take an optional First, step 3 can do step 3+4 in one go.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-04-28 17:34:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/78#issuecomment-1527880681  

E.g. https://godbolt.org/z/694MEEdh9. This solution is slightly wrong because it starts counting from 1 instead of 0, but that's not relevant for the purposes of mp_iota2. :-)  
  
https://godbolt.org/z/jobhGdqP9 would be the right one.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-05-11 23:30:03 UTC  
> Url: https://github.com/boostorg/mp11/issues/78#issuecomment-1544827092  

https://godbolt.org/z/r4E8ea8jz

---

## Comment 4

> Username: pdimov  
> Created at: 2023-05-14 02:29:27 UTC  
> Url: https://github.com/boostorg/mp11/issues/78#issuecomment-1546791900  

Implemented on develop (https://github.com/boostorg/mp11/commit/99070f7f5c92a7ffea03bf72804f971f52562015, https://godbolt.org/z/KPjef91q3).
