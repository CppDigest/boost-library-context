# #1155 - asynchronous http pipelining server [Closed]

> Username: mirkub  
> Created at: 2018-06-08 15:00:58 UTC  
> Updated at: 2018-07-09 08:37:09 UTC  
> Closed at: 2018-07-09 08:37:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1155  

### Version of Beast  
167  
  
### Steps necessary to reproduce the problem  
Use:  
./beast/example/advanced/server/advanced_server.cpp  
add some artificial delay (sleep) into `void handle_request()` function.  
Compile & Run the server.  
Start a client and fire multiple (20) HTTP requests (do the pipelining).  
  
Current behavior: The server doesn't process the requests in parallel but one by one.   
  
What would be the best approach to make it process the pipelined requests simultaneously?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-08 16:03:18 UTC  
> Updated at: 2018-06-08 16:03:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1155#issuecomment-395808049  

You want to process multiple requests concurrently, and make sure that the responses are sent in the correct order (matching the order of requests)? That can be accomplished by breaking up the problem in two parts:  
  
Part one: In `handle_request`, dispatch the work to a thread pool. Cache the send function and use it to supply the calculated response.  
  
Part two: Implement a send function which stores the response in an ordered queue, in the correct position for the matching request. If no response is currently being sent, then send the response at the head of the queue  
  
You will need to modify `handle_request` to accept an ordinal (integer) representing the request number, and also provide that integer to the send function so it can perform the correct ordering.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-07-08 16:07:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1155#issuecomment-403297948  

This issue has been open for a while with no activity, has it been resolved?
