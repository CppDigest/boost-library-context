# #117 - Issues with the definition of "awaitable". [Closed]

> Username: akrzemi1  
> Created at: 2023-09-23 09:39:25 UTC  
> Updated at: 2023-10-16 15:04:17 UTC  
> Closed at: 2023-10-16 15:04:17 UTC  
> Url: https://github.com/boostorg/cobalt/issues/117  

"awaitable" is defined in the docs in two places:  
  
1. In Design (https://klemens.dev/async/design.html)  
2. Along with the concept in https://klemens.dev/async/reference.html#awaitable  
  
The former says:  
  
 * An [awaitable](https://klemens.dev/async/reference.html#awaitable) is an expression that can be used with `co_await`.  
 * An **actual awaitable** is a type that can be co_await-ed from within any coroutine, like a delay operation.   
 * A `pseudo-awaitable` is one that can only be used in coroutines adding special functionality for it.  
  
The latter says:  
* An awaitable is an expression that can be used with co_await.  
* defines two concept that do not necessarily correspond to the definition.  
  
Here is my understanding of the terms.  
  
Whether an expression is a valid operand of `co_await` can only be determined in the context of a promise-type of a coroutine. This is because of `await_transform` that can turn awaitables into non-awaitables and vice versa. A concept that would really need to check if a type is a valid argument to a `co_await` would also have to take the promise-type as argument. But I suppose you do not need such concept in your library.  
  
I recommend that you use a term like *natively awaitable* which means, "a valid argument to `co_await` in any coroutine whose promise-type does not define `await_transform`" and rename the concept to `natively_awaitable` to indicate that you are not attempting to define "awaitable" in general.  
  
I do not think that introducing term "pseudo-awaitable" is the right way to go here. Any type, including `int`, is "pseudo-awaitable". Instead, I would recommend using the following intuition:  
  
> "A promise-type of a coroutine can nominate certain non-natively-awaitable types to awaitables through the use of [await_transform](http://link_needed). Promise-types in this library consistently nominate certain types as awaitables thus making them appear as contextual keywords."  
  
Maybe call that term *awaitable-keywords*.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-09-23 10:07:17 UTC  
> Url: https://github.com/boostorg/cobalt/issues/117#issuecomment-1732272864  

I also thing that the Design section of the docs should describe how this library uses `await_transform` (among other reasons, because it is very clever):  
  
 * for implementing *awaitable-keywords*  
 * for providing access to the selected parts of the promise-type  
 * for cancelation

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-10-05 00:05:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/117#issuecomment-1747816973  

I think pseudo-keyword is a better term here. and then remove the `actual awaitable`. An `awaitable` is a type that can be awaited sans an await_transform. Then it matches the concept, too.
