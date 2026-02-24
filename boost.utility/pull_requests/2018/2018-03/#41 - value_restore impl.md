# #41 value_restore impl [Closed]

> Username: olegabr  
> Created at: 2018-03-01 11:30:22 UTC  
> Updated at: 2020-06-15 15:50:12 UTC  
> Closed at: 2020-06-15 15:50:12 UTC  
> Url: https://github.com/boostorg/utility/pull/41  

The `boost::serialization::state_saver` rethinked: http://www.boost.org/doc/libs/1_66_0/libs/serialization/doc/state_saver.html  
  
usage pattern:  
  
```c++  
#include <boost/utility/value_restore.hpp>  
  
void func(A & a)  
    boost::value_restore_ptr<A> ptr = boost::make_value_restore_ptr(a);  
    ... // alter state of a by calling non-const functions  
    ... // call other functions  
    // original state of a automatically restored on exit  
}  
```  
  
The difference form the original `boost::serialization::state_saver` is that it is implemented as a `Deleter` concept and can be used with standard smart pointers. The `boost::value_restore_ptr` is actually a template alias for the `std::unique_ptr`, if available.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-03-01 13:40:00 UTC  
> Updated_at: 2018-03-01 13:41:06 UTC  
> Url: https://github.com/boostorg/utility/pull/41#issuecomment-369594115  

A new component like this should undergo a review first.  
  
http://www.boost.org/community/reviews.html  
  
Note that the component needs to be documented before it can be reviewed.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-06-15 15:50:12 UTC  
> Url: https://github.com/boostorg/utility/pull/41#issuecomment-644217036  

I'm going to close this as there seems to be no attempt to present this for a review or at least a discussion. I've also not seen much demand for a tool like this.  
  
Also, note that there is Boost.ScopeExit that can be used to perform actions upon leaving the scope. It's not clear how the component proposed here is better.

---
