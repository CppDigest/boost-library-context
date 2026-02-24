# #151 Removing the use of std::function from tensor. [Closed]

> Username: Neel-Shah-29  
> Created at: 2022-01-16 14:18:55 UTC  
> Updated at: 2022-01-26 18:08:13 UTC  
> Closed at: 2022-01-26 18:08:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/151  

This fixes #150  
 Removing the use of std::function from tensor.

---

## Comment 1

> Username: amitsingh19975  
> Created_at: 2022-01-16 15:44:10 UTC  
> Updated_at: 2022-01-16 15:46:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/151#issuecomment-1013899280  

Please, fix all the functions in that file. You need to do the same for all the functions. And at the end, please, also squash all the commits into a single one.

---

## Comment 2

> Username: Neel-Shah-29  
> Created_at: 2022-01-16 16:00:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/151#issuecomment-1013902079  

> Please, fix all the functions in that file. You need to do the same for all the functions. And at the end, please, also squash all the commits into a single one.  
Yes sure will do for other functions as well and squash the commits.  
I had just a question to be asked that the   
```std::function<void(SizeType r, PointerOut c, PointerIn a)> lambda;```  
``` lambda = [&lambda, na, wc, wa, pi](SizeType r, PointerOut c, PointerIn a)```  
needs to be changed to   
```auto lambda = [na, wc, wa, pi](auto const& self, SizeType r, PointerOut c, PointerIn a)```

---

## Comment 3

> Username: amitsingh19975  
> Created_at: 2022-01-16 16:01:19 UTC  
> Url: https://github.com/boostorg/ublas/pull/151#issuecomment-1013902358  

> Please, fix all the functions in that file. You need to do the same for all the functions. And at the end, please, also squash all the commits into a single one.  
> Yes sure will do for other functions as well and squash the commits.  
> I had just a question to be asked that the  
> `std::function<void(SizeType r, PointerOut c, PointerIn a)> lambda;`  
> ` lambda = [&lambda, na, wc, wa, pi](SizeType r, PointerOut c, PointerIn a)`  
> needs to be changed to  
> `auto lambda = [na, wc, wa, pi](auto const& self, SizeType r, PointerOut c, PointerIn a)`  
  
Yes.

---

## Comment 4

> Username: Neel-Shah-29  
> Created_at: 2022-01-17 07:34:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/151#issuecomment-1014220768  

I have squashed all commits to single one and changed all functions according to my understanding . Please have a look at it. Any mistakes if exists in the pull request will be resolved as soon as possible , Just notify me here. Thanks !

---

## Comment 5

> Username: Neel-Shah-29  
> Created_at: 2022-01-26 17:57:13 UTC  
> Url: https://github.com/boostorg/ublas/pull/151#issuecomment-1022448078  

```  
std::function<void(SizeType r, PointerOut c, PointerIn a)> lambda;    
lambda = [&lambda, n, wc, wa](SizeType r, PointerOut c, PointerIn a)  
```  
this statement has void return type so here in lambda the trailing return type is to be mentioned as void and above statement can be replaced by below one right?  
  
```  
auto lambda = [n, wc, wa](auto const& self, SizeType r, PointerOut c, PointerIn a)->void  
```

---
