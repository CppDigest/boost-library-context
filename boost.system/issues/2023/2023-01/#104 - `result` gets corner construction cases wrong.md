# #104 - `result` gets corner construction cases wrong [Closed]

> Username: pdimov  
> Created at: 2023-01-20 23:17:50 UTC  
> Updated at: 2023-01-22 19:36:37 UTC  
> Closed at: 2023-01-22 19:36:37 UTC  
> Url: https://github.com/boostorg/system/issues/104  

Per https://brevzin.github.io//c++/2023/01/18/optional-construction/, the corner cases of constructing `result<bool>` from `result<int>` (https://godbolt.org/z/9ebvPqsrx) and `result<result<int>>` from `result<int>` (https://godbolt.org/z/PMcdE4Yes) are not handled correctly.
