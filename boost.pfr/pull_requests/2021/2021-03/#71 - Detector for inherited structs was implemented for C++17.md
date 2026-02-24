# #71 Detector for inherited structs was implemented for C++17 [Merged]

> Username: denzor200  
> Created at: 2021-03-13 15:00:53 UTC  
> Updated at: 2021-03-28 16:39:59 UTC  
> Merged at: 2021-03-28 16:34:52 UTC  
> Closed at: 2021-03-28 16:34:52 UTC  
> Url: https://github.com/boostorg/pfr/pull/71  

Hello Anton. I recently tried pfr in my code. It is fantastic! The amount of work you have done is impressive.   
But i noticed, that your library has some problem with inherited structs. Let me offer my decision for it. Thanks.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-03-28 16:39:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/71#issuecomment-808922043  

Great idea and PR, many thanks!  
  
I've done some tweaking in https://github.com/boostorg/pfr/commit/ea4c6e85f734b0ed9f4233ba9654b877398fa948. Some compilers were not executing the body of the conversion operator in unevaluated contexts and static_assert was not triggered.

---
