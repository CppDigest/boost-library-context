# #410 - Logic in gcc.jam assumes relevant version is <major>.<minor>, but since gcc 5 it's just <major> [Closed]

> Username: pdimov  
> Created at: 2019-03-12 03:38:45 UTC  
> Updated at: 2019-04-01 17:45:23 UTC  
> Closed at: 2019-04-01 17:45:23 UTC  
> Url: https://github.com/boostorg/build/issues/410  

F.ex. https://github.com/boostorg/build/blob/f15a84a815bec7897d73fdace3bc4bcf219f9ccd/src/tools/gcc.jam#L186-L194

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-01 15:08:03 UTC  
> Url: https://github.com/boostorg/build/issues/410#issuecomment-478618472  

This causes f.ex. `using gcc : 7 : /usr/bin/g++-7 ;` to fail, as just reported.
