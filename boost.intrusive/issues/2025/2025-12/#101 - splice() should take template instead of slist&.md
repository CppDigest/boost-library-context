# #101 - splice() should take template instead of slist& [Open]

> Username: vinipsmaker  
> Created at: 2025-12-09 18:09:45 UTC  
> Updated at: 2025-12-09 18:10:13 UTC  
> Url: https://github.com/boostorg/intrusive/issues/101  

I have something like follows:  
  
```cpp  
class Parent : public slist_base_hook<>  
{  
    // virtual destructor, etc  
};  
  
class Child : public Parent  
{};  
  
slist<Parent> storage;  
slist<Child> temporaries;  
```  
  
I need to move all elements from `temporaries` to `storage` at the end of a function. I've tried to use `splice()`, but it doesn't work because it expects a `slist` of the same type as `*this`.
