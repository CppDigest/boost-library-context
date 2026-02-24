# #66 - Lack of bidimensional for_each [Closed]

> Username: allopislozano  
> Created at: 2019-12-05 11:05:09 UTC  
> Updated at: 2019-12-09 10:43:23 UTC  
> Closed at: 2019-12-09 10:43:23 UTC  
> Url: https://github.com/boostorg/algorithm/issues/66  

I think it would be nice to have a way to easily apply a binary operation to two zipped ranges (or maybe a Nary operation to n zipped ranges).    
It would be kind of  boost::accumulate but returning void and that would not accumulate along the container dimension.  
Example:  
https://godbolt.org/z/gWFvcn

---

## Comment 1

> Username: mclow  
> Created at: 2019-12-07 18:22:48 UTC  
> Url: https://github.com/boostorg/algorithm/issues/66#issuecomment-562874452  

Wouldn't the existing `for_each` work for a zipped range (using `ranges3`)?  
The operation would have to be unary - but it would take a tuple of values.

---

## Comment 2

> Username: mclow  
> Created at: 2019-12-07 18:23:26 UTC  
> Url: https://github.com/boostorg/algorithm/issues/66#issuecomment-562874489  

Or using `boost::range` :-)

---

## Comment 3

> Username: allopislozano  
> Created at: 2019-12-09 10:43:23 UTC  
> Url: https://github.com/boostorg/algorithm/issues/66#issuecomment-563174744  

Hi Marshall, thank you for your answer, indeed I think that's the best solution, hadn't though about that possibility: https://godbolt.org/z/VXk6cs  
  
by the way, I really like your talks on youtube :)
