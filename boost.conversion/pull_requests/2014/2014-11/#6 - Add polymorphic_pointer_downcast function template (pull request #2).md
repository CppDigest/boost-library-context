# #6 Add polymorphic_pointer_downcast function template (pull request #2) [Merged]

> Username: Boris-Rasin  
> Created at: 2014-11-09 13:49:19 UTC  
> Updated at: 2014-11-10 09:15:34 UTC  
> Merged at: 2014-11-10 09:15:34 UTC  
> Closed at: 2014-11-10 09:15:34 UTC  
> Url: https://github.com/boostorg/conversion/pull/6  

Existing polymorphic_downcast works with C++ built-in pointers only, while this new polymorphic_pointer_downcast is a more generic version with support for C++ built-in pointers, std::shared_ptr, boost::shared_ptr, boost::intrusive_ptr, etc.

---
