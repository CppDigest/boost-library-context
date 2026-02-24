# #1972 - need to return read response back to caller through async callback from on_read() [Closed]

> Username: ashjas  
> Created at: 2020-06-03 11:23:19 UTC  
> Updated at: 2020-06-12 12:33:24 UTC  
> Closed at: 2020-06-12 12:32:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1972  

Hi,  
I started out with the http_client_async.cpp example for this task.  
  
I intend to use beast for implementing a wrapper api, that my client can use.  
The client's requirement is of an ```async_send(request  req, callback cb)``` api, which will send the request and get the response of the request in the ```cb``` callback asynchronously.  
  
Which means, i need to somehow finally call ```cb``` through io_context once the response is successfully read in the on_read() handler in the example.  
  
How can i achieve this simple task?  
  
I also stumbled upon the echo_op.cpp example which basically creates a custom async operation, where it callbacks the custom completion_handler.Is this example the only answer to my quesion?  
Or is there a simpler way?  
  
Thanks.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-03 11:51:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1972#issuecomment-638146793  

You "call a callback through an io_context" by posting a completion handler to the io_context's executor.  
  
```  
auto ioc = net::io_context();  
auto e = ioc.get_executor();  
net::post(e, []() {  
 // some code here  
});  
```

---

## Comment 2

> Username: ashjas  
> Created at: 2020-06-04 13:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1972#issuecomment-638837874  

@madmongo1 Thanks, this worked.  
Though this solution works, but how does it compare with creating a custom async operation?  
Can you think of any disadvantages of using net::post over working a custom async operation as shown in echo_op.cpp example?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-06-04 14:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1972#issuecomment-638872164  

@ashjas A custom async operation, otherwise known as a composed operation, is essentially:  
- a function object  
- associated with an executor  
- passes itself as the handler to async operations that it initiates.  
- has captured a completion handler  
- during one of its invocations, it will complete by calling its captured completion handler, as if by post() to the associated executor.  
  
Fundamentally, the final completion *is*, or *is equivalent to* a `post(e, handler);`

---

## Comment 4

> Username: ashjas  
> Created at: 2020-06-05 06:46:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1972#issuecomment-639291989  

@madmongo1   
Thanks for yor inputs.  
Though my orig question is solved, i have one more doubt regarding beast usage with maintaining keep_alive sessions.  
  
I have seperated the the REST operation into two seperate classes:  
**connect_session** and **talk_session**.  
```  
class connect_session  
{  
//members:  
 mResolver;  
 mStream;  
 static io_context;  
//methods:  
on_resolve();  
on_connect()  
};  
```  
  
```  
class talk_session  
{  
//members:  
connect_session& conn;  
req;  
res;  
buffer;  
//methods:  
on_write();  
on_read();  
};  
```  
--------------  
So basically, i maintain a registry of connections mapped to "host:port" so that these connections can be reused for talking to and fro. specially benifitial for ssl connections. For every request, i create a seperate talk_session object so , the req/resp/buffer being overwritten etc, is out of the question.  
  
With the above implemented, i am facing the issue, where i get strange errors inside on_read()  
errors are like: bad version , header_limit exceeded etc.  
On inspection,i found that from the talk_session class, the on_write and on_read operations are simultaneously accessing the conn.mStream object for writng/reading, which needs to be fixed.  
  
So i think the solution to this is to post the talk_session async operations on the mStream's strand.  
so that the operations from talk_session are sequenced from multiple threads.  
  
to achieve this,how do i call  ```async_write()``` and ```async_read()``` using the mStream's strand for this sequencing to be in effect?  
  
i am already calling async_write in the standard way:  
```  
http::async_write(m_Conn.getStream(), req_,  
        beast::bind_front_handler(  
            &talk_session::on_write,  
            shared_from_this()));  
```  
So passing m_Conn.getStream() isnt sufficient for sequencing.  
  
Thanks.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-06-12 12:32:59 UTC  
> Updated at: 2020-06-12 12:33:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1972#issuecomment-643246353  

> Though my orig question is solved  
  
Great, so I'll close this issue.  
  
> to achieve this,how do i call async_write() and async_read() using the mStream's strand for this sequencing to be in effect?  
  
There are two constraints you must satisfy:  
  
1. there must be no concurrent access to an io object or stream - you have used a strand for this, so that's fine.  
2. Only *one* async_read and async_write may be outstanding at any one time. For reads that's not an issue since you normally perform an async_read and then dispatch on the result. For writes, if you are initiating the communications (rather than just responding to a request) you must implement a write queue. You would then pick items off the queue, async_write them, and in the handler, check the queue for the next write.
