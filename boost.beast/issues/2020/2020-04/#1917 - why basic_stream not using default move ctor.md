# #1917 - why basic_stream not using default move ctor? [Closed]

> Username: feverzsj  
> Created at: 2020-04-26 05:05:00 UTC  
> Updated at: 2020-08-30 20:28:34 UTC  
> Closed at: 2020-05-05 00:18:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1917  

[basic_stream.hpp#L678](https://github.com/boostorg/beast/blob/f5064e0c460664fa34fdad44e2379a2309d39685/include/boost/beast/core/impl/basic_stream.hpp#L678)  
  
```C++  
template<class Protocol, class Executor, class RatePolicy>  
basic_stream<Protocol, Executor, RatePolicy>::  
basic_stream(basic_stream&& other)  
    : impl_(boost::make_shared<impl_type>(  
        std::move(*other.impl_)))  
{  
    // VFALCO I'm not sure this implementation is correct...  
}  
```  
as `impl_` is already a shared_ptr, why not just use its move ctor?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-26 06:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1917#issuecomment-619490502  

Hmm, that does look suspect. I'll take a look.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-05-01 15:40:03 UTC  
> Updated at: 2020-05-01 15:42:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1917#issuecomment-622438983  

Hi @feverzsj ,  
  
Many thanks for raising this concern. Without keen-eyed contributions like this, maintaining Beast would be a more difficult job.  
  
After careful investigation, I can confirm that the implementation is (perhaps surprisingly) correct and necessary.  
  
The reason as a result of documented behaviour of basic_socket [here](https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream/basic_stream/overload3.html)  
  
> Following the move, the moved-from object is in the same state as if newly constructed.  
  
This matches the guarantees of Asio's socket objects. Specifically meaning that the socket/basic_stream will remain associated with the original default executor.  
  
If we simply moved the `impl_`, the next call to `get_executor()` on the moved-from basic_stream would result in a segfault.  
  
I'll update the comment in the source code to reflect this.  
  
R

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-30 20:28:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1917#issuecomment-683466808  

Because Beast does not always know how to construct a new `NextLayerType`.
