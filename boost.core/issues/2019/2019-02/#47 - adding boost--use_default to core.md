# #47 - adding boost::use_default to core [Closed]

> Username: HDembinski  
> Created at: 2019-02-22 10:09:30 UTC  
> Updated at: 2019-02-24 12:14:11 UTC  
> Closed at: 2019-02-23 12:59:31 UTC  
> Url: https://github.com/boostorg/core/issues/47  

Several libraries (iterators, histogram, range, gil, fusion) use a tag type called `use_default`. This is used as a generic default template parameter for types with several template parameters, e.g.  
```  
template <class T1 = use_default, class T2 = use_default, class T2 = use_default>  
struct my_type {};  
```  
The type originates from the iterator library, and it defined there in the boost namespace, `boost::use_default`. It is independently defined at least in fusion and histogram, which also have a need for default parameters in long type lists. It could be used other libraries as well, like container.  
  
I propose to move this type from iterators to core, because it is generically useful and also helps to unify interfaces of boost libraries.

---

## Comment 1

> Username: glenfe  
> Created at: 2019-02-22 23:37:09 UTC  
> Url: https://github.com/boostorg/core/issues/47#issuecomment-466585715  

See  4ea704e in Core and https://github.com/boostorg/iterator/pull/49 in Iterator (waiting on Travis and Appveyor to complete).

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-02-24 12:14:11 UTC  
> Url: https://github.com/boostorg/core/issues/47#issuecomment-466768620  

thank you!
