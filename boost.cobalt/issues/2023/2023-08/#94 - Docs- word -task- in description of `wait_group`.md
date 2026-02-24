# #94 - Docs: word "task" in description of `wait_group` [Closed]

> Username: akrzemi1  
> Created at: 2023-08-29 23:38:05 UTC  
> Updated at: 2023-09-04 17:45:11 UTC  
> Closed at: 2023-09-04 07:32:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/94  

The description of `wait_group`  
https://github.com/klemens-morgenstern/async/blob/develop/doc/reference/wait_group.adoc  
says it only works with `promise<void>` but it mentions the processing of "tasks". It is confusing, as the `task` is a different type in this library.   
Second, why does `wait_group` not work with `task<void>`? The first intuition is that `promise` and `type` only differ by the initial suspend.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-01 16:52:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/94#issuecomment-1703043322  

Well it needs to pick something so that I can put it in a vector.  Wrapping a task in a promise is a simple lambda.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2023-09-01 17:28:02 UTC  
> Url: https://github.com/boostorg/cobalt/issues/94#issuecomment-1703095052  

This looks like another argument in favour of keeping tasks and promisses in the same type.  
  
How does one convert a task into a promise with a lambda? Is there a conversion function, or do I need a yet another coroutine?  
  
Or maybe you need some type-erasure 8n order to be able to store either?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-09-04 02:22:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/94#issuecomment-1704522634  

```cpp  
[]<typename T>(task<T> t) -> promise<T> {co_return co_await t;}  
```  
  
Yes, you need another coro to wrap it.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2023-09-04 11:57:01 UTC  
> Url: https://github.com/boostorg/cobalt/issues/94#issuecomment-1705141472  

Maybe provide a function that converts a `task` to a promise?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-09-04 13:26:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/94#issuecomment-1705273682  

What is the use case?

---

## Comment 6

> Username: akrzemi1  
> Created at: 2023-09-04 17:45:10 UTC  
> Url: https://github.com/boostorg/cobalt/issues/94#issuecomment-1705575302  

When I need to a `task<>` into a `wait_group`.   
I personally never had a need to do it. But this is my first exposure to this library, and it seems to me from this conversation that someone might want to use it.
