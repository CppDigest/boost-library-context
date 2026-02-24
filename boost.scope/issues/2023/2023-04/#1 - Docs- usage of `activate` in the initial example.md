# #1 - Docs: usage of `activate` in the initial example [Closed]

> Username: akrzemi1  
> Created at: 2023-04-15 16:07:28 UTC  
> Updated at: 2023-04-15 16:51:31 UTC  
> Closed at: 2023-04-15 16:51:06 UTC  
> Url: https://github.com/boostorg/scope/issues/1  

The initial example in the docs uses:  
  
```c++  
it->activate(arg);  
```  
  
This is a bit confusing, given that guards have a member function `set_active`, one starts to wonder if `it` points to a guard. Any other name, like `start`, would be better.  
  
Anyway, is this a real life example? Wouldn't you rather create an automatic object, activate (or start) it there, and then move into the set? This way, you do not need a guard.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-15 16:43:17 UTC  
> Url: https://github.com/boostorg/scope/issues/1#issuecomment-1509890013  

`it` is not pointing to a scope guard but a user's object. Here, `activate` is not supposed to mean something specific, it's just a placeholder to show that you do something on the inserted object that may potentially throw. I can rename the method, no problem.  
  
> Anyway, is this a real life example?  
  
Yes, more or less. I've had code similar to this, where the object needed to be added to some sort of collection before it could be "activated", because it relied on the fact that it is present in the collection at that point.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-04-15 16:51:31 UTC  
> Url: https://github.com/boostorg/scope/issues/1#issuecomment-1509891452  

Done. Thanks.
