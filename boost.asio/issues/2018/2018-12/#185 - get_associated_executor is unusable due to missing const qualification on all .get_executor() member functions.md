# #185 - get_associated_executor is unusable due to missing const qualification on all .get_executor() member functions [Closed]

> Username: yuri-kilochek  
> Created at: 2018-12-31 18:41:20 UTC  
> Updated at: 2019-01-05 12:26:08 UTC  
> Closed at: 2019-01-05 12:26:08 UTC  
> Url: https://github.com/boostorg/asio/issues/185  

`get_associated_executor(t)` ultimately calls `t.get_executor()` for const-qualified `t` (see [here](https://github.com/boostorg/asio/blob/a6008b6427ddfc3222f83c53030c34c802c53b59/include/boost/asio/associated_executor.hpp#L54)). However none of the `.get_executor()` member functions defined in the library are const-qualified ([basic_io_object](https://github.com/boostorg/asio/blob/a6008b6427ddfc3222f83c53030c34c802c53b59/include/boost/asio/basic_io_object.hpp#L105), [basic_socket](https://github.com/boostorg/asio/blob/a6008b6427ddfc3222f83c53030c34c802c53b59/include/boost/asio/basic_socket.hpp#L277), [posix::descriptor](https://github.com/boostorg/asio/blob/a6008b6427ddfc3222f83c53030c34c802c53b59/include/boost/asio/posix/descriptor.hpp#L173), etc.), thus rendering it unusable. Or am I missing something?

---

## Comment 1

> Username: djarek  
> Created at: 2019-01-02 20:47:34 UTC  
> Updated at: 2019-01-02 20:48:37 UTC  
> Url: https://github.com/boostorg/asio/issues/185#issuecomment-450980350  

The associators ([async.reqmts.associator] in [N4771](https://wg21.link/n4771))  `associated_executor` and `associated_allocator` are only defined for types that are CompletionHandlers. The I/O executor is retrievable by calling `get_executor()` ([async.reqmts.async.io.exec]).  
  
Note: `get_associated_executor(t)` does not necessarily need to call `t.get_executor()`.

---

## Comment 2

> Username: yuri-kilochek  
> Created at: 2019-01-03 01:08:18 UTC  
> Url: https://github.com/boostorg/asio/issues/185#issuecomment-451032741  

I see. But then, what does _if not otherwise specified_ in [async.reqmts.async.io.exec]/1 mean, if not missing `.get_executor()` on the io object?  
  
And shouldn't `.get_executor()`s be const regardless?

---

## Comment 3

> Username: djarek  
> Created at: 2019-01-03 10:22:58 UTC  
> Url: https://github.com/boostorg/asio/issues/185#issuecomment-451104217  

That clause is there to allow operations to have their own special rules for determining the I/O executor. I don't think any operations in the TS actually make use of it.  
  
As for `const` qualified `get_executor()` - it can't be required to be const, because an I/O object is allowed to return an executor which modifies members of the I/O object, requiring `const` would necessitate the use of `mutable`.
