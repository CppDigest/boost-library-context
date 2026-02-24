# #443 - How to achieve data read timeout on local::stream_protocol::socket [Open]

> Username: raviteja-b  
> Created at: 2020-12-07 10:11:08 UTC  
> Updated at: 2020-12-07 10:11:08 UTC  
> Url: https://github.com/boostorg/boost/issues/443  

Basically i am using async_wait with wait_read flag to read data on socket when socket is ready for read.  
but this async_wait waits forever with wait_read flag if there is no data on socket.  
i wanted to have a timeout for this async_wait operation on local::stream_protocol::socket  
  
    stream_protocol::socket unixSocket;  
    this->unixSocket.async_wait(  
        stream_protocol::socket::wait_read,  
        [this, self(shared_from_this())](boost::system::error_code ec) {  
            if (ec)  
            {  
                 
               this->connection->close();  
                return;  
            }  
          doReadDataOnSocket();  
     }
