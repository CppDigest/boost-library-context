# #18 Avoid gcc 9 -Wdeprecated-copy warning (when you define the copy const… [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2019-06-28 15:58:25 UTC  
> Updated at: 2020-05-22 14:10:05 UTC  
> Closed at: 2020-05-22 14:10:04 UTC  
> Url: https://github.com/boostorg/bimap/pull/18  

…ructor, you have to define to copy assignment operator too in C++ >= 11).

---

## Comment 1

> Username: glenfe  
> Created_at: 2020-05-22 13:33:51 UTC  
> Url: https://github.com/boostorg/bimap/pull/18#issuecomment-632693005  

This would need to be done for all the iterators not just one of them. But I don't think this is the best solution. Rather than explicitly adding copy assignment operators, we should get rid of the explicit copy constructors.  
* When both copy constructor and copy assignment operator are implicitly provided it should eliminate the warnings.

---

## Comment 2

> Username: glenfe  
> Created_at: 2020-05-22 14:10:04 UTC  
> Url: https://github.com/boostorg/bimap/pull/18#issuecomment-632712880  

Addressed in 6b4d000a4fb38978c87a48c7c22344e61a33290f.

---
