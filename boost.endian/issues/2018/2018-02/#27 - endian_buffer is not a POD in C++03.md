# #27 - endian_buffer is not a POD in C++03 [Closed]

> Username: viboes  
> Created at: 2018-02-11 18:44:06 UTC  
> Updated at: 2020-10-07 17:55:18 UTC  
> Closed at: 2020-10-07 17:55:18 UTC  
> Url: https://github.com/boostorg/endian/issues/27  

Even when we define   
  
```c++  
#define BOOST_ENDIAN_FORCE_PODNESS  
#define  BOOST_ENDIAN_NO_CTORS  
```  
  
endian_buffer is not a POD, because it uses a protected data member.   
  
See https://wandbox.org/permlink/PSYwP0oCoJ9cNtkV  
  
Defining it as public in C++03 solves the issue.   
  
std::is_pod works in C++11 as the definition was relaxed.  
The problem is that boost::is_pod is based on C++03 definition of POD.  
  
Formal definition from the C++ standard (C++03 8.5.1 §1):  
>    An aggregate is an array or a class (clause 9) with no user-declared constructors (12.1), no private or protected non-static data members (clause 11), no base classes (clause 10), and no virtual functions (10.3).  
    
Formal definition from the C++ standard (C++03 9 §4):  
>    A POD-struct is an aggregate class that has no non-static data members of type non-POD-struct, non-POD-union (or array of such types) or reference, and has no user-defined copy assignment operator and no user-defined destructor. Similarly, a POD-union is an aggregate union that has no non-static data members of type non-POD-struct, non-POD-union (or array of such types) or reference, and has no user-defined copy assignment operator and no user-defined destructor. A POD class is a class that is either a POD-struct or a POD-union.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-29 16:38:07 UTC  
> Url: https://github.com/boostorg/endian/issues/27#issuecomment-487652195  

It's true that `endian_buffer` is not a C++03 POD, but there's a test that it can be used in a union, and this test passes. So I'm not sure that making the data member public is really necessary in practice.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-10-07 17:55:18 UTC  
> Url: https://github.com/boostorg/endian/issues/27#issuecomment-705098501  

Should be fixed in develop.
