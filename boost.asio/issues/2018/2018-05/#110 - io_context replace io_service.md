# #110 - io_context replace io_service [Closed]

> Username: snahmad  
> Created at: 2018-05-10 13:54:03 UTC  
> Updated at: 2020-12-30 00:51:10 UTC  
> Closed at: 2020-12-30 00:51:09 UTC  
> Url: https://github.com/boostorg/asio/issues/110  

Should we been using io_context instead of io_service in latest boost.

---

## Comment 1

> Username: djarek  
> Created at: 2018-05-10 22:07:39 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-388200746  

`io_service` is deprecated. Yes, you should use `io_context`. Beware that the "old" API is deprecated as well (e.g. `io_service.post()`, you should use `post(io_context, handler)`).

---

## Comment 2

> Username: superlambchops  
> Created at: 2018-09-24 15:18:25 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-424012815  

Is there a guide to converting a program from using io_context to use io_service?

---

## Comment 3

> Username: djarek  
> Created at: 2018-10-05 08:55:32 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-427293741  

`io_context` -> `io_service`  
`io_context.post()` -> `io_context.get_executor().post()`  
`io_context.dispatch()` -> `io_context.get_executor().dispatch()`  
  
`io_context::strand` -> `strand<io_context::executor_type>`  
  
there were also changes to composed operation customization hooks - there are only 2 now - `boost::asio::associated_allocator` and `boost::asio::associated_executor`, which default to looking for `get_allocator()`, `get_executor()`, T::allocator_type, T::executor_type members of the composed operation function object.  
  
This is not a complete list.

---

## Comment 4

> Username: GrumpyOldTroll  
> Created at: 2019-10-05 16:34:26 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-538665652  

Is there a doc outlining the reasons a non-backward-compatible change was required?  This is an unpleasant surprise, and pretty disruptive to existing projects using boost...

---

## Comment 5

> Username: DinoStray  
> Created at: 2019-11-08 07:24:24 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-551416517  

I feel terrible for the changes

---

## Comment 6

> Username: TorstenRobitzki  
> Created at: 2020-04-02 12:10:00 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-607806954  

@djarek In your list above, there is one thing missing. In earlier versions of asio, we could obtain the `io_context` from `get_io_service()` in serial_port or other IO objects. Now, `.get_executor().context()` returns the base class to `io_context` and a downcast is required. Is there an other solution to get the `io_context` from an existing IO object?

---

## Comment 7

> Username: thejohnfreeman  
> Created at: 2020-04-14 19:52:34 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-613648198  

> `io_context` -> `io_service`  
> `io_context.post()` -> `io_context.get_executor().post()`  
> `io_context.dispatch()` -> `io_context.get_executor().dispatch()`  
>   
> `io_context::strand` -> `strand<io_context::executor_type>`  
  
New user here. Is this supposed to mean "the left expression supersedes the right expression" or "change occurrences of the left expression to the one on the right"?

---

## Comment 8

> Username: ivan-v-kush  
> Created at: 2020-05-23 16:39:55 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-633090913  

> Is there a doc outlining the reasons a non-backward-compatible change was required? This is an unpleasant surprise, and pretty disruptive to existing projects using boost...  
  
I think due to Network TS. But now appears the question, why the WG have   
choosen `io_context `  
  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0112r0.html

---

## Comment 9

> Username: ghost  
> Created at: 2020-12-30 00:51:08 UTC  
> Url: https://github.com/boostorg/asio/issues/110#issuecomment-752289478  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#615](https://github.com/chriskohlhoff/asio/issues/615).
