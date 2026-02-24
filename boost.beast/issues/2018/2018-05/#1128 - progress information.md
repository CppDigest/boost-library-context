# #1128 - progress information [Closed]

> Username: yorns  
> Created at: 2018-05-15 13:57:18 UTC  
> Updated at: 2018-05-16 06:59:21 UTC  
> Closed at: 2018-05-16 06:05:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1128  

Hi and thanks for beast!  
  
I took some time to study your api and got easy and fast success with most things. On issue (or maybe additional feature) I would like to have is a progress information. That can be helpful with bigger files or slow connections.  
  
What I did recently is using the `on_chunk_body()` callback to gather information during data transfer. However, I prefere not to interfer/rewrite any e.g. filewrite but have this information additionally. I use file_body for writing the data to a file. Unfortunately for this case, the implementation looks like this:  
  
```  
    std::size_t  
    on_chunk_body_impl(  
        std::uint64_t remain,  
        string_view body,  
        error_code& ec)  
    {  
        if(cb_b_)  
            return cb_b_(remain, body, ec);  
        return wr_.put(boost::asio::buffer(  
            body.data(), body.size()), ec);  
    }  
  
```   
so the put() is never called, when the callback is set. For a small little helper like progress information, this is odd (but not for general usecases).   
  
Do you see any other possibility for getting e.g. continuous progress information?  
  
I thought about calling wr_.put from the callback, but i did not find any possibility to access wr_ from the outside.  
  
Thanks in advance!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-15 14:13:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1128#issuecomment-389182023  

> What I did recently is using the on_chunk_body() callback  
  
This callback is only invoked when Transfer-Encoding specifies **chunked** as the last encoding in the list.  
  
> Do you see any other possibility for getting e.g. continuous progress information?  
  
Yes, you're way overthinking this :) Just call `http::write_some` in a loop with the serializer until `serializer::is_done()` returns `true`.

---

## Comment 2

> Username: yorns  
> Created at: 2018-05-15 14:40:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1128#issuecomment-389191622  

hi Vinnie,  
  
thanks for the fast reply. Can you give me some few more words?   
  
Starting from your http_client_async example (with `http::response_parser<http::file_body>` )  
  
the on_write() method would contain the following:  
  
```  
        http::async_read(socket_, buffer_, res_,  
            std::bind(  
                &session::on_read,  
                shared_from_this(),  
                std::placeholders::_1,  
                std::placeholders::_2));  
  
```  
How is the best way to implement your suggestion?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-15 15:11:20 UTC  
> Updated at: 2018-05-15 16:20:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1128#issuecomment-389203151  

Oh, sorry, you are trying to **receive** a file not send it! Just use `read_some` in a loop until the parser returns `true` for `is_done`:  
  
```  
while(! p.is_done()) // p is a parser  
    http::read_some(sock, buffer, p);  
```

---

## Comment 4

> Username: yorns  
> Created at: 2018-05-16 06:05:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1128#issuecomment-389405081  

sure, async_read_some would do that. Having a special progress information callback might be more convinient for some developers, as then everything would be done implicitly by calling async_read and asio handlers.  
But never mind, things work for me.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-16 06:59:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1128#issuecomment-389415437  

Beast is a low-level library. It provides the raw building blocks, and then you can assemble them in any way you desire in order to suit your needs.
