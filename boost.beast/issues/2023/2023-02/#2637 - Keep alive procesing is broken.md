# #2637 - [awaitable example] Keep alive procesing is broken [Closed]

> Username: ROCKFAL1  
> Created at: 2023-02-10 17:26:37 UTC  
> Updated at: 2023-02-10 21:42:22 UTC  
> Closed at: 2023-02-10 21:42:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2637  

I took one of your examples to show the problem and ask for the right solution.  
  
So, we have [session procesing loop](https://github.com/boostorg/beast/blob/develop/example/http/server/awaitable/http_server_awaitable.cpp#L213) with keep-alive handling. Suppose that one request comes with the header "Connection: keep-alive".  Then [keep_alive](https://github.com/boostorg/beast/blob/develop/example/http/server/awaitable/http_server_awaitable.cpp#L239) flag sets to true. Due `do try {}` construction we get into an endless cycle of exceptions (if the client does not make another request with the cancellation of "Connection: close"). This is a resource leak.  
  
What is the main reason? All `async_*` functions return immediately. So, I want to find solution that will hold `async_read[_headers]` operation for N secons and if bytes/headers do not arrive, I want to receive an error and terminate the session processing.   
  
I hope I have described problem in sufficient detail and clearly :)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-02-10 17:36:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2637#issuecomment-1426129176  

> What is the main reason? All async_* functions return immediately.   
  
What do you mean by that? What do you mean by client cancellation?  
  
  
There is indeed a bug in that the catch block should have a `break` statement if it's end of stream, to break the loop.

---

## Comment 2

> Username: ROCKFAL1  
> Created at: 2023-02-10 17:53:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2637#issuecomment-1426145752  

> What do you mean by that? What do you mean by client cancellation?  
  
I will try to explain in more detail  
We have `keep_alive == true`, after that we respond to client using `async_write` and go to the second iteration of `for(;;)` loop. Then we have the `async_read` function called, which throws an `end_of_stream` exception. And it happens endlessly.  
  
> There is indeed a bug in that the catch block should have a break statement if it's end of stream, to break the loop.  
  
It's not that simple. If we do this, there will be no point in processing `keep-alive`.  Because on the second iteration `for(;;)` it's not a fact that we will receive another request from the client.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-02-10 17:57:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2637#issuecomment-1426149379  

Why is it not that simple? End of stream should break the for-loop and close the connection.

---

## Comment 4

> Username: ROCKFAL1  
> Created at: 2023-02-10 17:58:32 UTC  
> Updated at: 2023-02-10 17:59:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2637#issuecomment-1426151011  

If we do this, then our `keep-alive` session will probably last even less than one millisecond :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-02-10 21:23:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2637#issuecomment-1426363022  

end of stream means that the remote closed the connection, you cannot "keep alive" after that.
