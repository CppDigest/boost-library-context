# #77 - heap_allocation for a class without a specific new operator calls the class' destructor [Closed]

> Username: pdziekan  
> Created at: 2017-11-06 12:22:26 UTC  
> Updated at: 2017-11-08 17:16:03 UTC  
> Closed at: 2017-11-08 17:16:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/77  

Since the commit https://github.com/boostorg/serialization/commit/69ecae6919b417be2b2558aefffea97fbe50d4a8 , the method doesnt_have_new_operator::invoke_delete(T * t) not only frees memory, but also calls the destructor of class T.  
  
This causes compilation errors when using BS with blitz++ arrays, because the destructor of blitz::MemoryBlock is protected.  
What is the rationale behind the commit? Please note that the commit did not change the behaviour of the class if DONT_USE_HAS_NEW_OPERATOR is defined.  
Same concerns were expressed by @qingl97 in his comment under the discussed commit.
