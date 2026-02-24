# #13 intrusive_ptr: add converting ctor for intrusive_ptr<U> with move semantics [Merged]

> Username: jtwang83  
> Created at: 2015-03-16 00:27:53 UTC  
> Updated at: 2016-05-20 22:11:53 UTC  
> Merged at: 2016-05-20 22:11:53 UTC  
> Closed at: 2016-05-20 22:11:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/13  

Analagous to template <class U> intrusive_ptr(intrusive_ptr<U> const&).  
Requires a template friend declaration for intrusive_ptr<U>.

---
