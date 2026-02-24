# #5 =add polymorphic_pointer_downcast function template [Closed]

> Username: Boris-Rasin  
> Created at: 2014-11-08 22:14:38 UTC  
> Updated at: 2014-11-09 13:53:44 UTC  
> Closed at: 2014-11-09 13:53:44 UTC  
> Url: https://github.com/boostorg/conversion/pull/5  

Existing polymorphic_downcast works with C++ built-in pointers only, while this new polymorphic_pointer_downcast is a more generic version with support for C++ built-in pointers, std::shared_ptr, boost::shared_ptr, boost::intrusive_ptr, etc.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-11-09 10:22:39 UTC  
> Updated_at: 2014-11-09 10:30:36 UTC  
> Url: https://github.com/boostorg/conversion/pull/5#issuecomment-62298402  

Looks good, however I'd like to see more tests with `std::shared_ptr`, `boost::shared_ptr`, `boost::intrusive_ptr`.   
  
Usage example in documentation also required: it is unclear what type must be used for cast? Is it `shared_ptr<Derived>` or just `Derived`?  
  
Also please make pull requests to boostorg:develop branch instead of master.

---

## Comment 2

> Username: Boris-Rasin  
> Created_at: 2014-11-09 13:53:44 UTC  
> Url: https://github.com/boostorg/conversion/pull/5#issuecomment-62304096  

I've created updated pull request to boostorg:develop branch instead of master:  
https://github.com/boostorg/conversion/pull/6  
Closing this request.

---
