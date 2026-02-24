# #23 - which detail::forced_unwind should user code propagate: boost::coroutines2 or boost::context? [Closed]

> Username: bezkrovatki  
> Created at: 2018-11-06 13:34:17 UTC  
> Updated at: 2020-04-24 09:30:59 UTC  
> Closed at: 2020-04-24 09:30:59 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/23  

Hello,  
  
the [documentation](https://www.boost.org/doc/libs/1_68_0/libs/coroutine2/doc/html/coroutine2/coroutine/asymmetric.html) says:  
  
>  Code executed by coroutine-function must not prevent the propagation of the detail::forced_unwind exception  
  
... and the subsequent example propagates boost::coroutines2::detail::forced_unwind.  
  
However, simple grep over boost/coroutine2 sources shows boost::coroutines2::detail::forced_unwind is not referenced anywhere except the place it was declared.  
Pull/push coroutines implementation catches and re-throws the boost::context::detail::forced_unwind instead.  
  
So which exception a user code should propagate?  
  
Background  
I've patched boost.asio to use boost.coroutine2 for our product in order to allow our code to be instrumented with ASan. Both the code and ASan instrumentation seem to work after the migration however one of our unit tests fails. It explicitly throws  boost::coroutines2::detail::forced_unwind from the coroutine to check whether the exception is propagated correctly by our code and expects the coroutine collapses. This worked perfectly with boost.coroutine (v1), but crashes with unhandled exception for boost.coroutine2.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:30:59 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/23#issuecomment-618907509  

done
