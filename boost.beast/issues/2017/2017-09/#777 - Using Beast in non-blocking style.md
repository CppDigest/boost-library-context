# #777 - Using Beast in non-blocking style [Closed]

> Username: ksergey  
> Created at: 2017-09-10 19:28:11 UTC  
> Updated at: 2017-09-12 18:49:39 UTC  
> Closed at: 2017-09-12 18:49:38 UTC  
> Url: https://github.com/boostorg/beast/issues/777  

Is it possible to use Beast websocket stream in non-blocking style?  
  
i.e.  
```c++  
boost::system::error_code ec;  
auto sz = stream.read(..., ec);  
if (ec == boost::asio::error::would_block) {  
   continue;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-10 19:47:16 UTC  
> Updated at: 2017-09-10 19:53:52 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328366846  

It might work, but I have never done any testing. It is on my to-do list (there's an open issue somewhere around here). If you would like to try it out and let me know, that would be cool. Don't forget to set the socket into non-blocking mode first.  
  
If it doesn't work, it probably only needs a little bit of busy work in the synchronous operation code in order to make it happen.

---

## Comment 2

> Username: ksergey  
> Created at: 2017-09-11 19:32:15 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328634630  

@vinniefalco It's not work. I think it because any unsuccessful read (and maybe write) set stream status to failed and on the next read `error_code` become `operation_aborted`  
  
see read.ipp `read_some`  
```  
    if(! check_open(ec))                                                                                                      
        return 0;                                                                                                             
loop:                                                                                                                         
...  
            auto const bytes_transferred =                                                                                    
                stream_.read_some(                                                                                            
                    rd_buf_.prepare(read_size(                                                                                
                        rd_buf_, rd_buf_.max_size())),                                                                        
                    ec);                                                                                                      
            if(! check_ok(ec))                                                                                                
                return bytes_written;                                                                                         
            rd_buf_.commit(bytes_transferred);                                                                                
```  
  
stream.hpp  
```  
    bool                                                                                                                      
    check_open(error_code& ec)                                                                                                
    {                                                                                                                         
        if(status_ != status::open)                                                                                           
        {                                                                                                                     
            ec = boost::asio::error::operation_aborted;                                                                       
            return false;                                                                                                     
        }                                                                                                                     
        ec.assign(0, ec.category());                                                                                          
        return true;                                                                                                          
    }                                                                                                                         
                                                                                                                              
    bool                                                                                                                      
    check_ok(error_code& ec)                                                                                                  
    {                                                                                                                         
        if(ec)                                                                                                                
        {                                                                                                                     
            if(status_ != status::closed)                                                                                     
                status_ = status::failed;                                                                                     
            return false;                                                                                                     
        }                                                                                                                     
        return true;                                                                                                          
    }                                                                                                                         
```  
  
I think `check_ok` should ignore `ec` in case of `boost::asio::error::would_block` and `boost::asio::error::try_again` and maybe other errors (in case of SSL socket)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-11 19:44:53 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328637941  

>I think check_ok should ignore ec in case of boost::asio::error::would_block and boost::asio::error::try_again and maybe other errors (in case of SSL socket)  
  
That sounds reasonable. Have you tried making this change?

---

## Comment 4

> Username: ksergey  
> Created at: 2017-09-12 07:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328763131  

```c++  
    bool                                                                                                                                             
    check_ok(error_code& ec)                                                                                                                         
    {                                                                                                                                                
        if(ec)                                                                                                                                       
        {                                                                                                                                            
            if (ec != boost::asio::error::would_block && ec != boost::asio::error::try_again) {                                                      
                if(status_ != status::closed)                                                                                                        
                    status_ = status::failed;                                                                                                        
                return false;                                                                                                                        
            }                                                                                                                                        
        }                                                                                                                                            
        return true;                                                                                                                                 
    }      
```  
  
This code fix the issue in my case (short messages via SSL stream). But I'm not sure it will work in other cases (i.e. big messages).  
  
Also I'm not tested websocket `write`.  
  
I will continue investigation

---

## Comment 5

> Username: ksergey  
> Created at: 2017-09-12 07:32:15 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328765969  

Sorry, it's not a fix.   
`read_some` still loop inside  
```c++  
        while(! parse_fh(rd_fh_, rd_buf_, code))                                                                                                     
        {                                                                                                                                            
            if(code != close_code::none)                                                                                                             
            {                                                                                                                                        
                // _Fail the WebSocket Connection_                                                                                                   
                do_fail(code, error::failed, ec);                                                                                                    
                return bytes_written;                                                                                                                
            }                                                                                                                                        
            auto const bytes_transferred =                                                                                                           
                stream_.read_some(                                                                                                                   
                    rd_buf_.prepare(read_size(                                                                                                       
                        rd_buf_, rd_buf_.max_size())),                                                                                               
                    ec);                                                                                                                             
            if(! check_ok(ec))                                                                                                                       
                return bytes_written;                                                                                                                
            rd_buf_.commit(bytes_transferred);                                                                                                       
        }                                                                                                                                            
```

---

## Comment 6

> Username: ksergey  
> Created at: 2017-09-12 07:34:34 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328766601  

`check_ok` should be  
```c++  
    bool                                                                                                                                             
    check_ok(error_code& ec)                                                                                                                         
    {                                                                                                                                                
        if(ec)                                                                                                                                       
        {                                                                                                                                            
            if (ec != boost::asio::error::would_block && ec != boost::asio::error::try_again) {                                                      
                if(status_ != status::closed)                                                                                                        
                    status_ = status::failed;                                                                                                        
            }                                                                                                                                        
            return false;                                                                                                                            
        }                                                                                                                                            
        return true;                                                                                                                                 
    }      
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-09-12 15:15:32 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328884634  

Sometimes a call to `stream::read` is going to need to perform write operations, how will we handle that case? What if the call to write returns "would block?" The way the code is currently structured, there's no way to restart the operation.

---

## Comment 8

> Username: ksergey  
> Created at: 2017-09-12 18:49:38 UTC  
> Url: https://github.com/boostorg/beast/issues/777#issuecomment-328946446  

Looks like the same problem with `stream::write` and `stream::handshake` (i.e. sent half of message).   
  
I think it's not possible without interface change.
