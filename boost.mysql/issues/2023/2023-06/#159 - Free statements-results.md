# #159 - Free statements/results [Closed]

> Username: Mecanik  
> Created at: 2023-06-06 05:09:07 UTC  
> Updated at: 2023-06-06 15:17:27 UTC  
> Closed at: 2023-06-06 15:17:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/159  

Thanks for the awesome library! I do have a question and concern since I am trying to use it. Assuming that I want to keep the connection with MySQL opened to perform continuous work (multi-threaded), how do you free results?  
  
At the moment all I can see is .close() and .close_statement(), but there is no implementation like .free() or .free_results().  
  
Please advise.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-06-06 08:14:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/159#issuecomment-1578152174  

The results destructor does the work. You don't need to do anything on your side to free a `results` object.  
  
The reason why prepared statements need a special `close_statement` function is because statements are really handles to server-side objects. `close_statement` sends a message to the server instructing to deallocate the server-side object. This is an operation that may block and fail, so running it from a destructor is nor adequate.  
  
A similar reasoning applies to `close` - it will send a message to the server instructing to kill the connection.  
  
TL;DR: results memory freeing happens automatically as part of its destructor.

---

## Comment 2

> Username: Mecanik  
> Created at: 2023-06-06 08:33:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/159#issuecomment-1578187337  

That sounds great, so action needed from the user; just do your queries. Thanks!

---

## Comment 3

> Username: anarthal  
> Created at: 2023-06-06 15:17:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/159#issuecomment-1578963526  

Yep, no action needed unless you're preparing statements dynamically, in which case `close_statement` is needed.  
  
I'm gonna close this issue. Should you have more questions, please feel free to ask!
