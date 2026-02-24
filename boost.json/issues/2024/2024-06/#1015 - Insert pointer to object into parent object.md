# #1015 - Insert pointer to object into parent object [Closed]

> Username: MGraefe  
> Created at: 2024-06-26 12:25:30 UTC  
> Updated at: 2024-06-27 18:22:24 UTC  
> Closed at: 2024-06-27 18:22:24 UTC  
> Url: https://github.com/boostorg/json/issues/1015  

In my project, we construct a lot of `json::object`s from scratch and then serialize it. It often happens, that a function creates a single 'inner' object, lets call it `inner`, which then has to be wrapped in multiple slightly different parent objects. These parent objects are then serialized.  
  
So basically I am doing the following:  
```cpp  
const object inner = createInner();  
object parentA = createParent("foo");  
object parentB = createParent("bar");  
parentA["inner"] = inner; // copy happening :(  
parentB["inner"] = inner; // copy happening :(  
doSomething(serialize(parentA));  
doSomething(serialize(parentB));  
```  
  
The problem here is that I have to create a copy of `inner` for each `parent` prior to the serialization. I can't *move* `inner` into `parent` because it's needed by multiple parents.  
What I would like to do instead, is to just "link" `inner` into the parent with a (shared) pointer or something similar, so I can still serialize the whole parent object in a single call, but I don't have to copy the inner object. Basically I want to do something like this:  
```cpp  
parentA["inner"] = &inner;  
```  
This is more or less how it works in JavaScript where everything is done with references and to actually "deep-copy" an object you have to do `JSON.parse(JSON.stringify(obj))`.  
  
Is there a way to solve this problem with `boost::json`? Obviously the method above doesn't work for good reasons, but I'm wondering if I can do some magic with the memory resource objects, a custom type or a custom serializer?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-06-26 13:09:58 UTC  
> Url: https://github.com/boostorg/json/issues/1015#issuecomment-2191655612  

I'm afraid, I don't see how this can be done with Boost.JSON. As an alternative, I can suggest keeping your data in a custom type, and then directly serialize from that type into JSON. We don't yet support direct serialisation, but I'm working on it. I suspect, I won't finish the work before the deadline for 1.86 release, so this means you'll have to wait until 1.87.  
  
If you do need to stick to `json::object`s, then I don't have even a potential solution for you.

---

## Comment 2

> Username: MGraefe  
> Created at: 2024-06-27 18:22:24 UTC  
> Url: https://github.com/boostorg/json/issues/1015#issuecomment-2195410309  

Thanks for the insights! I will experiment a bit with custom types, maybe I can make it work. The thing is, we're looking for an alternative to nlohmann's library, mainly because of the high memory usage of JSON objects. Having to copy the objects is even worse. Currently I'm serializing the inner and parent objects separately and then merge the raw strings....that works but it's really fragile and cumbersome.  
  
Anyway, thanks again and I think this can be closed as I don't have any concrete idea how to implement it.
