# #34 - -Wdeprecated-copy on gcc9 [Closed]

> Username: vinniefalco  
> Created at: 2019-01-12 14:57:36 UTC  
> Updated at: 2019-03-05 10:26:55 UTC  
> Closed at: 2019-03-05 10:26:39 UTC  
> Url: https://github.com/boostorg/intrusive/issues/34  

This is showing up on gcc9 now:  
https://wandbox.org/permlink/zsA9pNqip1v6bZod  
  
Doesn't seem to happen on earlier versions.

---

## Comment 1

> Username: djarek  
> Created at: 2019-01-12 15:17:52 UTC  
> Url: https://github.com/boostorg/intrusive/issues/34#issuecomment-453755450  

https://github.com/boostorg/intrusive/blob/develop/include/boost/intrusive/detail/list_iterator.hpp#L65  
This constructor is treated as a copy constructor if `IsConst == false` and is a converting constructor if `IsConst == true`,

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-03-05 10:26:55 UTC  
> Url: https://github.com/boostorg/intrusive/issues/34#issuecomment-469627148  

Many thanks for the report.
