# #2077 - advanced_server_flex example questions [Closed]

> Username: iburyl  
> Created at: 2020-09-02 12:02:46 UTC  
> Updated at: 2020-09-02 13:29:29 UTC  
> Closed at: 2020-09-02 13:29:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2077  

1) class queue  
```  
        template<bool isRequest, class Body, class Fields>  
        void  
        operator()(http::message<isRequest, Body, Fields>&& msg)  
```  
Is it correct that in the context of the queue, isRequest shall always be `false`?  
  
2) class http_session  
  
`on_read()` function has only one path for error code checking, but within the logic of this example, timeout is also a valid path and correct way to behave in this case is to just return. Is it correct?  
  
```  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return derived().do_eof();  
  
        // ~Timeout path~  
        if(ec == beast::condition::timeout)  
        {  
            // do_eof() is not applicable here - we already dropped this connection  
            return;  
        }  
  
        if(ec)  
            return fail(ec, "read");  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-02 12:44:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2077#issuecomment-685709829  

Q1: In this case, yes. The queue is probably over-engineered for this example as it is designed to queue any kind of message.  
  
Q2: you could certainly add another path for handling timeouts. Unlike the queue, the example is taking a simple approach to error handling.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-02 12:55:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2077#issuecomment-685716849  

>  it is designed to queue any kind of message.  
Yes precisely

---

## Comment 3

> Username: iburyl  
> Created at: 2020-09-02 13:29:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2077#issuecomment-685737617  

Thanks. I'll close the issue, but timeout branch in example will likely help with understanding of correct execution flow.
