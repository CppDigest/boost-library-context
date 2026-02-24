# #2739 - In example: Error and note [Closed]

> Username: heretic13  
> Created at: 2023-09-13 14:30:15 UTC  
> Updated at: 2024-01-02 17:58:59 UTC  
> Closed at: 2024-01-02 17:58:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2739  

### Version of Beast  
At Boost 1_83.  
  
I have some comments about this example.  
https://www.boost.org/doc/libs/1_83_0/libs/beast/example/advanced/server/advanced_server.cpp  
  
1. Note.  
  
In function  
void queue_write(http::message_generator response)  
You do push_back first.  
You then ensure that no work is done by checking size()==1 for the vector.  
  
Abstracting from the container type, the empty() operation takes the same amount of time as size() or faster. It is more profitable to first check the vector for empty() and store the result in a variable. Then do push_back(). Then, depending on the value of the variable, execute do_write();  
  
2. Error.  
In function  
bool do_write()  
  
After checking !response_queue_.empty(), you retrieve the element from the vector and start the asynchronous operation.  
  
If you look at the body of the queue_write() function, you can see that it controls the absence of an asynchronous operation by comparing the queue size with 1 (after adding an element).  
Which is incorrect.  
  
Example:  
  
Start. queue size=0  
1. queue_write(). queue size=1  
2. call do_write()  
starting an asynchronous operation. queue size=0  
3. queue_write(). queue size=1  
4. call do_write()  
starting an asynchronous operation. queue size=0  
  
We did not wait for the previous asynchronous operation to complete and started a new one.

---

## Comment 1

> Username: heretic13  
> Created at: 2023-09-13 14:41:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1717770998  

3. Error.  
  
Function  
// Returns `true` if the caller may initiate a new read  
bool do_write()  
  
Further in the text, the function returns the sign that the queue is completely full.  
  
That is, if the queue is full, we can start a new reading??

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-09-13 15:26:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1717856393  

2. The `queue_write` checks if the queue was empty before being called - why is that wrong?  
3. Yes. What's the issue?

---

## Comment 3

> Username: heretic13  
> Created at: 2023-09-13 15:37:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1717873919  

> 2. The `queue_write` checks if the queue was empty before being called - why is that wrong?  
> 3. Yes. What's the issue?  
  
2. Documenatation  
https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__async_write.html  
"The program must ensure that the stream performs no other writes until this operation completes."  
The example above CAN runs MULTIPLE async_write operations.  
  
3. In the real world, we make a queue to limit the amount of data sent.  
  
If I correctly understood the author’s idea, he assumed a request->response sequence with a quantity limit. The request has been processed, put into the submission queue, and we are ready to accept a new request. We shouldn't accept a new request if we can't save the result to the queue.  
This rule is broken here.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-09-14 00:01:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1718462500  

2. How?  
3. Ok, so why's that an error?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-09-14 00:20:17 UTC  
> Updated at: 2023-09-14 00:20:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1718478311  

if the queue is full we should not read the next request until there is room for a response, this will allow TCP/IP's natural application-level flow control to work properly

---

## Comment 6

> Username: heretic13  
> Created at: 2023-09-14 07:46:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1718934958  

> 2. How?  
  
Read carefully. I gave an example above.  
Example (repeat):  
  
Start. queue size=0  
  
1. queue_write(). queue size=1  
2. call do_write()  
starting an asynchronous operation. queue size=0  
3. queue_write(). queue size=1  
4. call do_write()  
starting an asynchronous operation. queue size=0  
We did not wait for the previous asynchronous operation to complete and started a new one.  
  
  
> 3. Ok, so why's that an error?  
  
Because in your (original) example we start a new read request when the send queue is full. This defeats the purpose of limiting the send queue.

---

## Comment 7

> Username: heretic13  
> Created at: 2023-09-14 07:47:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1718936932  

> if the queue is full we should not read the next request until there is room for a response, this will allow TCP/IP's natural application-level flow control to work properly  
  
This is right. Good idea.  
But this doesn't work in your example.

---

## Comment 8

> Username: ashtum  
> Created at: 2024-01-01 13:47:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1873335720  

@heretic13, I can confirm that there is a bug in this line:  
https://github.com/boostorg/beast/blob/0d1e2d64f8a02d0c883760154019a7e60246480f/example/advanced/server/advanced_server.cpp#L445  
This leads to a situation where we initiate another `async_write` while there is an ongoing one.

---

## Comment 9

> Username: ashtum  
> Created at: 2024-01-01 13:55:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2739#issuecomment-1873338131  

This problem exists in all advanced server examples:  
https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex-awaitable/advanced_server_flex_awaitable.cpp  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp  
https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp
