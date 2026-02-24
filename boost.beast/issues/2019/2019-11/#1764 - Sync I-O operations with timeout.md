# #1764 - Sync I/O operations with timeout [Closed]

> Username: chreniuc  
> Created at: 2019-11-11 13:21:34 UTC  
> Updated at: 2020-08-11 10:15:22 UTC  
> Closed at: 2019-12-18 14:27:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1764  

Version of boost we are using: 1.71.0  
  
Hi,  
we want to impement sync methods for `ws` with timeout, we already did this for the `tcp` socket  using the async methods, just like in  [this example from boost asio](https://github.com/boostorg/asio/blob/develop/example/cpp11/timeouts/blocking_tcp_client.cpp#L65) and we wanted to try the same thing for websocket.  
  
We got to something like this:  
  
```c++  
// io_context is per ws object  
  
// ws is created like this(it is used only from one thread):  
ws = make_shared<boost::beast::websocket::stream<  
    boost::beast::ssl_stream<boost::asio::ip::tcp::socket>>>(io_context, ctx);  
//----------------------------------------------------------------------------//  
void websocket::send_request(const std::string &request,  
                             const unsigned int timeout)  
{  
  ws->async_write(boost::asio::buffer(request),  
                  std::bind([&ec](const boost::system::error_code  
                                    &result_error) { ec = result_error; },  
                            std::placeholders::_1));  
  run(std::chrono::seconds(timeout));  
  if (ec == boost::beast::error::timeout) {  
    cout << "Timeout W " << endl;  
  } else {  
    cout << ec.message() << endl;  
  }  
}  
//----------------------------------------------------------------------------//  
utils::result websocket::wait_read(const unsigned int timeout)  
{  
  // Number of bytes copied into the buffer.  
  boost::beast::flat_buffer message_ptr;  
  ::boost::system::error_code ec;  
  ws->async_read(message_ptr,  
                 [&rcount, &ec](const boost::system::error_code &result_error,  
                                std::size_t result_size) {  
                   ec = result_error;  
                   rcount = static_cast<int>(result_size);  
                 });  
  run(std::chrono::seconds(timeout));  
  
  if (ec == boost::system::errc::connection_aborted  
      || ec == boost::system::errc::operation_canceled) {  
    return utils::result::TIMEOUT;  
  }  
  
  if (ec) {  
    utils::result::ERROR;  
  }  
  
  return utils::result::SUCCESS;  
}  
//----------------------------------------------------------------------------//  
void websocket::run(const std::chrono::steady_clock::duration &timeout)  
{  
  cout << "State of  the ws: " << ws->is_open() << endl;  
  cout << "State of the tcp: " << boost::beast::get_lowest_layer(*ws).is_open()  
       << endl;  
  
  // Restart the io_context, as it may have been left in the "stopped" state  
  // by a previous operation. If the previous operation did not timeout.  
  this->io_context.restart();  
  
  // Block until the asynchronous operation has completed, or timed out. If  
  // the pending asynchronous operation is a composed operation, the deadline  
  // applies to the entire operation, rather than individual operations on  
  // the socket.  
  this->io_context.run_for(timeout);  
  
  // If the asynchronous operation completed zsuccessfully then the io_context  
  // would have been stopped due to running out of work. If it was not  
  // stopped, then the io_context::run_for call must have timed out.  
  if (!this->io_context.stopped()) {  
    // Cancel all async operations.  
    boost::beast::get_lowest_layer(*ws).cancel();  
  
    // Run the io_context again until the operation completes. This will run  
    // the completion handler for the async operations and will set the  
    // error code to operation aborted.  
    this->io_context.run();  
  }  
}  
```  
  
The app that is using this code, reads some requests from some log files and sends them to a server. Each request has a timestamp, the app needs to send them at the same interval as in the log file. So the app sends a request and then does a read with timeout, the timeout being the the interval between requests. So there are cases when that read won't read anything and we need to stop it after the time interval has passed.  
  
Summary: We want to make a read with timeout, and then we want to continue doing operations on the ws instance.  
  
Running the code from above, we encountered a problem: if we had a I/O operation that did timeout, `boost::beast::get_lowest_layer(*ws).cancel();` would be called and afterwards every other async operation would finish immediately and would return the error code `Operation canceled`.  
  
After some digging we found that the open status of the `ws` is set to `false`, while the state of the underlying socket is `true`, just like someone says [here](https://github.com/boostorg/beast/issues/1117#issue-320598431).  
  
Now we are stuck, how does one do sync operations with timeout on a websocket object? The websocket object would be used from one thread, it will have its own io_context(just like you do it, when you are using only sync methods). If a call does timeout(we do not treat it as an error and close the socket), the websocket should still be usable(using `cancel` makes it unusable).  
  
Thanks :D

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-11 13:25:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1764#issuecomment-552444687  

You can't cancel websocket operations and then expect to be able to continue doing reads and writes later, as it leaves the stream in a valid but undefined state.

---

## Comment 2

> Username: chreniuc  
> Created at: 2019-11-11 13:54:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1764#issuecomment-552454725  

Hi @vinniefalco thanks for taking the time to respond :smile:   
  
>You can't cancel websocket operations and then expect to be able to continue doing reads and writes later, as it leaves the stream in a valid but undefined state.  
  
We're well aware of that after reading your comment from [here](https://github.com/boostorg/beast/issues/1117#issuecomment-386889501).  
  
We've opened this issue to see if there are other people who encountered this problem and if there are, we're curious on how did they manage to do sync read and write with timeout on a websocket object.  
  
A method that we will try next, is getting the underlying socket and setting the `SO_RCVTIMEO` and `SO_SNDTIMEO` like this:  
```c++  
typedef boost::asio::detail::socket_option::integer<SOL_SOCKET, SO_RCVTIMEO> rcv_timeout_option;  
boost::beast::get_lowest_layer(*ws).set_option(rcv_timeout_option{ 200 });  
  
ws->read(...);  
```

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-12-11 14:03:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1764#issuecomment-564556070  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-12-18 14:27:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1764#issuecomment-567054016  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 5

> Username: himacro  
> Created at: 2020-08-06 04:18:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1764#issuecomment-669673907  

> Hi @vinniefalco thanks for taking the time to respond smile  
>   
> > You can't cancel websocket operations and then expect to be able to continue doing reads and writes later, as it leaves the stream in a valid but undefined state.  
>   
> We're well aware of that after reading your comment from [here](https://github.com/boostorg/beast/issues/1117#issuecomment-386889501).  
>   
> We've opened this issue to see if there are other people who encountered this problem and if there are, we're curious on how did they manage to do sync read and write with timeout on a websocket object.  
>   
> A method that we will try next, is getting the underlying socket and setting the `SO_RCVTIMEO` and `SO_SNDTIMEO` like this:  
>   
> ```c++  
> typedef boost::asio::detail::socket_option::integer<SOL_SOCKET, SO_RCVTIMEO> rcv_timeout_option;  
> boost::beast::get_lowest_layer(*ws).set_option(rcv_timeout_option{ 200 });  
>   
> ws->read(...);  
> ```  
  
Hi @chreniuc, I'm facing the same issue as you. Would you please tell me whether you have succeeded by this way? Thanks!

---

## Comment 6

> Username: chreniuc  
> Created at: 2020-08-11 10:15:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1764#issuecomment-671859979  

Hi @himacro, we didn't. We've investigated a lot. At first I was stubborn and didn't listen to Vinnie, but he's right, it can't be done. We've redesigned our server, and used async operations.  
  
We've tried with `socket.set_option(receive_tm_options);` and `socket.set_option(send_tm_options);`, just like I've said in the previous comment, but it didn't work.  
  
You should also read this issue: https://github.com/boostorg/beast/issues/893
