# #1955 - basic_stream must be used with strand? [Closed]

> Username: feverzsj  
> Created at: 2020-05-14 17:56:19 UTC  
> Updated at: 2020-05-14 18:38:09 UTC  
> Closed at: 2020-05-14 18:01:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1955  

even if there is no concurrent access to `basic_stream` object, the race condition may occur, as long as its executor is not a strand or single thread.  
  
It seems caused by the timeout mechanisms of `basic_stream`.  
  
in timeout_handle:  
  
    auto sp = wp.lock();  
    if(! sp)  
        return;  
  
    if(tick < state.tick) // could be a stale read, while state.tick has changed.  
        return;  
  
    BOOST_ASSERT(tick == state.tick);  
  
    BOOST_ASSERT(! state.timeout);  
    sp->close();  
    state.timeout = true;  
  
in xxx_op:  
  
    if(state().timer.expiry() != never())  
    {  
        ++state().tick;  
  
        auto const n = state().timer.cancel();  
  
        if(n == 0) // timeout_handler either has finished or is still running  
        {  
            if(state().timeout) // could be a stale read, while timeout_handler has closed or is closing the socket  
            {  
                ec = beast::error::timeout;  
                state().timeout = false;  
            }  
        }  
        else  
        {  
            BOOST_ASSERT(n == 1);  
            BOOST_ASSERT(! state().timeout);  
        }  
    }
