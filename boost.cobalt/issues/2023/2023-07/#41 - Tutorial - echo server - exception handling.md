# #41 - Tutorial > echo server > exception handling [Closed]

> Username: anarthal  
> Created at: 2023-07-24 08:45:17 UTC  
> Updated at: 2023-07-24 09:36:41 UTC  
> Closed at: 2023-07-24 09:36:41 UTC  
> Url: https://github.com/boostorg/cobalt/issues/41  

> The operation is wrapped a try-catch block, because it should just stop & close the socket for any error (including cancellation).  
  
I'd rephrase to  
  
> When using the `use_op` completion token, I/O errors are translated into C++ exceptions. Additionally, if the coroutine gets cancelled (e.g. because the user hit Ctrl-C), an exception will be raised, too. Under these conditions, we print the error and exit the loop.
