# #75 - enable_shared_from erroneously allows visibility of enable_shared_from_this() [Closed]

> Username: cmazakas  
> Created at: 2020-04-01 18:10:16 UTC  
> Updated at: 2020-04-02 21:04:34 UTC  
> Closed at: 2020-04-02 21:04:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/75  

https://godbolt.org/z/7Z_fhD  
  
The above code allows:  
```cpp  
auto ptr = x.shared_from_this();  
```  
and when the user then attempt to use:  
```cpp  
ptr->speak();  
```  
, there is a compilation failure that `speak` is not a method on the target pointer.  
  
Instead, `x.shared_from_this()` should not compile in the first place.
