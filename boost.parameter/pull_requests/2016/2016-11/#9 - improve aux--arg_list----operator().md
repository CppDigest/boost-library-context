# #9 improve aux::arg_list<>::operator[]() [Merged]

> Username: rick68  
> Created at: 2016-11-23 01:54:03 UTC  
> Updated at: 2016-12-24 06:27:16 UTC  
> Merged at: 2016-12-24 06:27:16 UTC  
> Closed at: 2016-12-24 06:27:16 UTC  
> Url: https://github.com/boostorg/parameter/pull/9  

Add the assert to avoid ugly message when invoked `arg_list<>::operator[](lazy_default<>)` with `maybe<>`

---
